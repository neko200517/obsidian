## 概要

ドメイン層⇒インフラストラクチャ層の依存方向を
インフラストラクチャ層⇒ドメイン層に修正する

## infrastructur/repositoryディレクトリの作成

```bash
mkdir src/infrastructur/repository 
```

## GameMySQLRepositoryクラスの作成 

GameRepositoryの実装を移動

```bash
mkdir src/infrastructur/repository/game
touch src/infrastructur/repository/game/gameMySQLRepository.ts
```

```ts
import mysql from 'mysql2/promise';
import { GameGateway } from './gameGateway';
import { Game } from '../../../domian/model/game/game';
import { GameRepository } from '../../../domian/model/game/gameRepository';

const gameGateway = new GameGateway();

export class GameMySQLRepository implements GameRepository {
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

## GameRepositoryの修正

interfaceに変更

```ts
import mysql from 'mysql2/promise';
import { Game } from './game';

export interface GameRepository {
  // 最初のゲームを取得
  findLatest(conn: mysql.Connection): Promise<Game | undefined>;
  // ゲームを保存
  save(conn: mysql.Connection, game: Game): Promise<Game>;
}
```

## その他

以下のファイルをsrc/infrastructur/repository/gameに移動 
- gameGateway.ts
- gameRecord.ts

## GameResultMySQLRepositoryクラスの作成 

GameResultRepositoryの実装を移動

```bash
mkdir src/infrastructur/repository/gameResult
touch src/infrastructur/repository/gameResult/gameResultMySQLRepository.ts
```

```ts
import mysql from 'mysql2/promise';
import { GameResult } from '../../../domian/model/gameResult/gameResult';
import { GameResultGateway } from './gameResultGateway';
import { toWinnerDisc } from '../../../domian/model/gameResult/winnerDisc';
import { GameResultRepository } from '../../../domian/model/gameResult/gameResultRepository';

const gameResultGateway = new GameResultGateway();

export class GameResultMySQLRepository implements GameResultRepository {
  async findForGameId(
    conn: mysql.Connection,
    gameId: number
  ): Promise<GameResult | undefined> {
    const gameResultRecord = await gameResultGateway.findForGameId(
      conn,
      gameId
    );

    if (!gameResultRecord) {
      return undefined;
    }

    return new GameResult(
      gameResultRecord.gameId,
      toWinnerDisc(gameResultRecord.winnerDisc),
      gameResultRecord.endAt
    );
  }

  async save(conn: mysql.Connection, gameResult: GameResult): Promise<void> {
    await gameResultGateway.insert(
      conn,
      gameResult.gameId,
      gameResult.winnerDisc,
      gameResult.endAt
    );
  }
}
```

## GameResultRepositoryの修正

interfaceに変更

```ts
import mysql from 'mysql2/promise';
import { GameResult } from './gameResult';

export interface GameResultRepository {
  findForGameId(
    conn: mysql.Connection,
    gameId: number
  ): Promise<GameResult | undefined>;
  save(conn: mysql.Connection, gameResult: GameResult): Promise<void>;
}
```

## その他

以下のファイルをsrc/infrastructur/repository/gameResultに移動 
- gameResultGateway.ts 
- gameResultRecord.ts 

## TurnMySQLRepositoryクラスの作成 

turnRepositoryの実装を移動

```bash
mkdir src/infrastructur/repository/turn
touch src/infrastructur/repository/turn/turnMySQLRepository.ts
```

```ts
import mysql from 'mysql2/promise';
import { TurnGateway } from './turnGateway';
import { SquareGateway } from './squareGateway';
import { MoveGateway } from './moveGateway';
import { Turn } from '../../../domian/model/turn/turn';
import { toDisc } from '../../../domian/model/turn/disc';
import { Board } from '../../../domian/model/turn/board';
import { Move } from '../../../domian/model/turn/move';
import { Point } from '../../../domian/model/turn/point';
import { DomainError } from '../../../domian/error/domainError';
import { TurnRepository } from '../../../domian/model/turn/turnRepository';

const turnGateway = new TurnGateway();
const moveGateway = new MoveGateway();
const squareGateway = new SquareGateway();

export class TurnMySQLRepository implements TurnRepository {
  // 指定したターンを取得
  async findForGameIdAndTurnCount(
    conn: mysql.Connection,
    gameId: number,
    turnCount: number
  ): Promise<Turn> {
    const turnRecord = await turnGateway.findForGameIdAndTurnCount(
      conn,
      gameId,
      turnCount
    );
    if (!turnRecord) {
      throw new DomainError(
        'SpecifiedTurnNotFound',
        'Specified turn not found'
      );
    }

    // 盤面を取得
    const squareRecords = await squareGateway.findForTurnId(
      conn,
      turnRecord.id
    );

    // 盤面の値を 8 x 8 の2次元配列にマッピング
    const board = Array.from(Array(8)).map(() => Array.from(Array(8)));
    squareRecords.forEach((s) => {
      board[s.y][s.x] = s.disc;
    });

    // 現在のMoveを取得
    const moveRecord = await moveGateway.findForTurnId(conn, turnRecord.id);
    let move: Move | undefined;
    if (moveRecord) {
      move = new Move(
        toDisc(moveRecord.disc),
        new Point(moveRecord.x, moveRecord.y)
      );
    }

    const nextDisc =
      turnRecord.nextDisc === null ? undefined : toDisc(turnRecord.nextDisc);

    return new Turn(
      gameId,
      turnCount,
      nextDisc,
      move,
      new Board(board),
      turnRecord.endAt
    );
  }

  // 次のターンを保存
  async save(conn: mysql.Connection, turn: Turn) {
    const turnRecord = await turnGateway.insert(
      conn,
      turn.gameId,
      turn.turnCount,
      turn.nextDisc,
      turn.endAt
    );

    // 盤面を保存
    await squareGateway.insertAll(conn, turnRecord.id, turn.board.discs);

    // moveの登録
    if (turn.move) {
      await moveGateway.insert(
        conn,
        turnRecord.id,
        turn.move.disc,
        turn.move.point.x,
        turn.move.point.y
      );
    }
  }
}
```

## TurnRepositoryの修正

interfaceに変更

```ts
import mysql from 'mysql2/promise';
import { Turn } from './turn';

export interface TurnRepository {
  // 指定したターンを取得
  findForGameIdAndTurnCount(
    conn: mysql.Connection,
    gameId: number,
    turnCount: number
  ): Promise<Turn>;

  // 次のターンを保存
  save(conn: mysql.Connection, turn: Turn): Promise<void>;
}
```

## その他

以下のファイルをsrc/infrastructur/repository/gameResultに移動
- turnGateway.ts 
- turnRecord.ts 
- moveGateway.ts 
- moveRecord.ts 
- squareGateway.ts 
- squareRecord.ts
