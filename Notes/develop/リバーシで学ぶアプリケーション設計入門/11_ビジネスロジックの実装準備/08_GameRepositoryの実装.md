## ドメイン層のディレクトリ作成の方針

### 技術的な観点でディレクトリを分離

modelとrepositoryの配下に大量のファイルが置かれることになる

```bash
domain/
├── model
└── repository
```

### ドメインモデルの観点でディレクトリを分離

ドメインモデルとしてまとまった単位で整理される

```bash
domain/
├── game
└── turn
```

リバーシではドメインモデルの観点でディレクトリを作成する。

##  ディレクトリを作成して各ファイルを移動

以下のような構成にする

```bash
├─game
│      game.ts
│      gameRepository.ts
│
└─turn
        board.ts
        disc.ts
        move.ts
        point.ts
        turn.ts
        turnRepository.ts
```

## Gameクラスの作成

```ts
export class Game {
  constructor(private _id: number | undefined, private _startedAt: Date) {}

  get id() {
    return this._id;
  }

  get startedAt() {
    return this._startedAt;
  }
}
```

## GameRepositoryクラスの作成 

```ts
import mysql from 'mysql2/promise';
import { GameGateway } from '../../dataaccess/gameGateway';
import { Game } from './game';

const gameGateway = new GameGateway();

export class GameRepository {
  // 最初のゲームを取得
  async findLatest(conn: mysql.Connection): Promise<Game | undefined> {
    const gameRecord = await gameGateway.findLatest(conn);

    if (!gameRecord) {
      return undefined;
    }

    return new Game(gameRecord.id, gameRecord.startedAt);
  }

  // ゲームを保存
  async save(conn: mysql.Connection, game: Game): Promise<Game> {
    const gameRecord = await gameGateway.insert(conn, game.startedAt);

    return new Game(gameRecord.id, gameRecord.startedAt);
  }
}
```

## GameServiceクラスの修正

ゲームを保存する箇所を変更

```ts
import { connectMySql } from '../dataaccess/connection';

import { Game } from '../domian/game/game';
import { GameRepository } from '../domian/game/gameRepository';
import { firstTurn } from '../domian/turn/turn';
import { TurnRepository } from '../domian/turn/turnRepository';

const turnRepository = new TurnRepository();
const gameRepository = new GameRepository();

export class GameService {
  async startNewGame() {
    const now = new Date();
    const conn = await connectMySql();

    try {
      await conn.beginTransaction();

      // ゲームを保存
      const game = await gameRepository.save(conn, new Game(undefined, now));
      if (!game.id) {
        throw new Error('game.id not exist');
      }

      // ゲームの最初のターンを登録
      const turn = firstTurn(game.id, now);
      await turnRepository.save(conn, turn);

      // コミット
      await conn.commit();
    } catch {
      await conn.rollback();
      throw new Error();
    } finally {
      await conn.end();
    }
  }
}
```

## TurnServiceクラスの修正 

最新のゲームを取得する箇所を修正

```ts
import { connectMySql } from '../dataaccess/connection';
import { GameRepository } from '../domian/game/gameRepository';
import { toDisc } from '../domian/turn/disc';
import { Point } from '../domian/turn/point';
import { TurnRepository } from '../domian/turn/turnRepository';

const turnRepository = new TurnRepository();
const gameRepository = new GameRepository();

class FindLatestGameTurnByTurnCountOutput {
  constructor(
    private _turnCount: number,
    private _board: number[][],
    private _nextDisc: number | undefined,
    private _winnerDisc: number | undefined
  ) {}

  get turnCount() {
    return this._turnCount;
  }

  get borad() {
    return this._board;
  }

  get nextDisc() {
    return this._nextDisc;
  }

  get winnerDisc() {
    return this._winnerDisc;
  }
}

export class TurnService {
  async findLatestGameTurnByTurnCount(
    turnCount: number
  ): Promise<FindLatestGameTurnByTurnCountOutput> {
    const conn = await connectMySql();
    try {
      // 最新ゲームを取得
      const game = await gameRepository.findLatest(conn);
      if (!game) {
        throw new Error('Latest game not found');
      }
      if (!game.id) {
        throw new Error('game.id not exist');
      }

      // 指定したターンを取得
      const turn = await turnRepository.findForGameIdAndTurnCount(
        conn,
        game.id,
        turnCount
      );

      // レスポンスを作成
      return new FindLatestGameTurnByTurnCountOutput(
        turnCount,
        turn.board.discs,
        turn.nextDisc,
        undefined
      );
    } finally {
      conn.end();
    }
  }

  async registerTurn(turnCount: number, disc: number, x: number, y: number) {
    const conn = await connectMySql();
    try {
      await conn.beginTransaction();

      // 最新ゲームを取得
      const game = await gameRepository.findLatest(conn);
      if (!game) {
        throw new Error('Latest game not found');
      }
      if (!game.id) {
        throw new Error('game.id not exist');
      }

      // 1つ前のターンを取得する
      const previousTurnCount = turnCount - 1;
      const previousTurn = await turnRepository.findForGameIdAndTurnCount(
        conn,
        game.id,
        previousTurnCount
      );

      // 石を置く
      const newTurn = previousTurn.placeNext(toDisc(disc), new Point(x, y));

      // 次のターンを保存
      await turnRepository.save(conn, newTurn);

      // コミット
      await conn.commit();
    } catch {
      await conn.rollback();
      throw new Error();
    } finally {
      await conn.end();
    }
  }
}
```