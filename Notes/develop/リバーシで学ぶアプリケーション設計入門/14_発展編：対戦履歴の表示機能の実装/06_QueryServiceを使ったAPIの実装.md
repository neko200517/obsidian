---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - typescript
  - express
  - 設計
  - オブジェクト指向
  - DI
  - 3層アーキテクチャ
  - デザインパターン
aliases:
  - <% tp.file.title %>
---

## 構成

以下の構成で新しく query ディレクトリを作成する。

```bash
./src
├─application
│  ├─query
│  │      findLastGamesQueryService.ts # ←追加
│  │
│  └─useCase
│          findLastGamesUseCase.ts # ←追加
│
├─domian
|
├─infrastructure
│  ├─query
│  │      findLastGamesMySQLQueryService.ts # ←追加
│  │
│  └─repsitory
│
└─presentation
```

## findLastGamesQueryService クラス

データモデルやインタフェースの定義

```ts
import mysql from 'mysql2/promise';

export class FindLastGamesQueryModel {
  constructor(
    private _gameId: number,
    private _darkMoveCount: number,
    private _lightMoveCount: number,
    private _winnerDisc: number,
    private _startedAt: Date,
    private _endAt: Date
  ) {}

  get gameId() {
    return this._gameId;
  }

  get darkMoveCount() {
    return this._darkMoveCount;
  }

  get lightMoveCount() {
    return this._lightMoveCount;
  }

  get winnerDisc() {
    return this._winnerDisc;
  }

  get startedAt() {
    return this._startedAt;
  }

  get endAt() {
    return this._endAt;
  }
}

export interface FindLastGamesQueryService {
  query(
    conn: mysql.Connection,
    limit: number
  ): Promise<FindLastGamesQueryModel[]>;
}
```

## FindLastGamesMySQLQueryService クラス

SQL の実装

```ts
import mysql from 'mysql2/promise';
import { FindLastGamesQueryService } from '../../application/query/findLastGamesQueryService';
import { FindLastGamesQueryModel } from '../../application/query/findLastGamesQueryService';

export class findLastGamesMySQLQueryService
  implements FindLastGamesQueryService
{
  async query(
    conn: mysql.Connection,
    limit: number
  ): Promise<FindLastGamesQueryModel[]> {
    const selectResult = await conn.execute<mysql.RowDataPacket[]>(
      `
select
 max(g.id) as game_id,
 sum(case when m.disc = 1 then 1 else 0 end) as dark_move_count,
 sum(case when m.disc = 2 then 1 else 0 end) as light_move_count,
 max(gr.winner_disc) as winner_disc,
 max(g.started_at) as started_at,
 max(gr.end_at) as end_at
from games g
left join game_results gr on gr.game_id = g.id
left join turns t on t.game_id = g.id
left join moves m on m.turn_id = t.id
group by g.id
order by g.id desc
limit ?
      `,
      [limit.toString()]
    );

    const records = selectResult[0];

    return records.map(
      (r) =>
        new FindLastGamesQueryModel(
          r['game_id'],
          r['dark_move_count'],
          r['light_move_count'],
          r['winner_disc'],
          r['started_at'],
          r['end_at']
        )
    );
  }
}
```

## findLastGamesUseCase.ts

ユースケースの実装

```ts
import { connectMySql } from '../../infrastructure/connection';
import {
  FindLastGamesQueryModel,
  FindLastGamesQueryService,
} from '../query/findLastGamesQueryService';

const FIND_COUNT = 10;

export class FindLastGamesUseCase {
  constructor(private _findLastGamesMySQLService: FindLastGamesQueryService) {}

  async run(): Promise<FindLastGamesQueryModel[]> {
    const conn = await connectMySql();
    try {
      return await this._findLastGamesMySQLService.query(conn, FIND_COUNT);
    } finally {
      await conn.end();
    }
  }
}
```

## presentation/gameRouter.ts

依存性の注入、実装の組み込み、レスポンスの返却

```ts
// 中略

const findGamesQueryUseCase = new FindLastGamesUseCase(
  new findLastGamesMySQLQueryService()
);

interface GetGamesResponseBody {
  games: {
    id: number;
    darkMoveCount: number;
    lightMoveCount: number;
    winnerDisc: number;
    startedAt: Date;
    endAt: Date;
  }[];
}

// 中略

// ゲーム履歴を取得する
gameRouter.get(
  '/api/games',
  async (req, res: express.Response<GetGamesResponseBody>) => {
    const output = await findGamesQueryUseCase.run();

    const responseBodyGames = output.map((g) => {
      return {
        id: g.gameId,
        darkMoveCount: g.darkMoveCount,
        lightMoveCount: g.lightMoveCount,
        winnerDisc: g.winnerDisc,
        startedAt: g.startedAt,
        endAt: g.endAt,
      };
    });

    const responseBody = { games: responseBodyGames };

    res.json(responseBody);
  }
);
```
