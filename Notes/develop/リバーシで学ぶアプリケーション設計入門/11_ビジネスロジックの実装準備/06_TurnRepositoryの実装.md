## Repositoryパターンの追加

domain層にRepositoryを追加
⇒dataaccess層をinfrastructureに変更

![[Pasted image 20240131075734.png]]

## TurnRepsitoryクラス

```bash
touch domain/turnRepository.ts
```

```ts
import mysql from 'mysql2/promise';
import { TurnGateway } from '../dataaccess/turnGateway';
import { SquareGateway } from '../dataaccess/squareGateway';
import { MoveGateway } from '../dataaccess/moveGateway';
import { Turn } from './turn';
import { toDisc } from './disc';
import { Board } from './board';
import { Move } from './move';
import { Point } from './point';

const turnGateway = new TurnGateway();
const moveGateway = new MoveGateway();
const squareGateway = new SquareGateway();

export class TurnRepository {
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
      throw new Error('Specified turn not found');
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

    let move: Move | undefined = undefined;

    if (moveRecord) {
      move = new Move(
        toDisc(moveRecord.disc),
        new Point(moveRecord.x, moveRecord.y)
      );
    }

    return new Turn(
      gameId,
      turnCount,
      toDisc(turnRecord.nextDisc),
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

## TurnServiceクラスの修正

TurnRepositoryパターンを導入してすっきりと実装する。

```ts
import { connectMySql } from '../dataaccess/connection';
import { GameGateway } from '../dataaccess/gameGateway';
import { toDisc } from '../domian/disc';
import { Point } from '../domian/point';
import { TurnRepository } from '../domian/turnRepository';

const gameGateway = new GameGateway();

const turnRepository = new TurnRepository(); // 追加

// 中略

export class TurnService {
  async findLatestGameTurnByTurnCount(
    turnCount: number
  ): Promise<FindLatestGameTurnByTurnCountOutput> {
    const conn = await connectMySql();
    try {
      // 最新ゲームを取得
      const gameRecord = await gameGateway.findLatest(conn);
      if (!gameRecord) {
        throw new Error('Latest game not found');
      }

      // 指定したターンを取得
      const turn = await turnRepository.findForGameIdAndTurnCount(
        conn,
        gameRecord.id,
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
      const gameRecord = await gameGateway.findLatest(conn);
      if (!gameRecord) {
        throw new Error('Latest game not found');
      }

      // 1つ前のターンを取得する
      const previousTurnCount = turnCount - 1;
      const previousTurn = await turnRepository.findForGameIdAndTurnCount(
        conn,
        gameRecord.id,
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