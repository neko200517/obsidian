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

## TurnRecord クラス

```bash
touch dataacess/TurnRecord.ts
```

```ts
export class TurnRecord {
  constructor(
    private _id: number,
    private _gameId: number,
    private _turnCount: number,
    private _nextDisc: number,
    private _endAt: Date
  ) {}

  get id() {
    return this._id;
  }

  get nextDisc() {
    return this._nextDisc;
  }
}
```

## TurnGateway クラス

```bash
touch dataacess/TurnGateway.ts
```

```ts
import mysql from 'mysql2/promise';
import { TurnRecord } from './turnRecord';

export class TurnGateway {
  // 指定したターンを取得
  async findForGameIdAndTurnCount(
    conn: mysql.Connection,
    gameId: number,
    turnCount: number
  ): Promise<TurnRecord | undefined> {
    const tunrnSelectResult = await conn.execute<mysql.RowDataPacket[]>(
      'select id, game_id, turn_count, next_disc, end_at from turns where game_id = ? and turn_count = ?',
      [gameId, turnCount]
    );
    const record = tunrnSelectResult[0][0];

    if (!record) {
      return undefined;
    }

    return new TurnRecord(
      record['id'],
      record['game_id'],
      record['turn_count'],
      record['next_disc'],
      record['end_at']
    );
  }

  // ゲームの最初のターンを登録
  async insert(
    conn: mysql.Connection,
    gameId: number,
    turnCount: number,
    nextDisc: number,
    endAt: Date
  ): Promise<TurnRecord> {
    const turnnInsertResult = await conn.execute<mysql.ResultSetHeader>(
      'insert into turns (game_id, turn_count, next_disc, end_at) values (?, ?, ?, ?)',
      [gameId, turnCount, nextDisc, endAt]
    );
    const turnId = turnnInsertResult[0].insertId;

    return new TurnRecord(turnId, gameId, turnCount, nextDisc, endAt);
  }
}
```
