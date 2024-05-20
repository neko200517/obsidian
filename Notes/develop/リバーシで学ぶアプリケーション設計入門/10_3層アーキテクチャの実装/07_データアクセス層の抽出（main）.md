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

## main

既存の main.ts に実装する。

```ts
// src/main.ts

import express from 'express';
import morgan from 'morgan';
import 'express-async-errors';
import mysql from 'mysql2/promise';
import { GameGateway } from './dataaccess/gameGateway';
import { TurnGateway } from './dataaccess/turnGateway';
import { SquareGateway } from './dataaccess/squareGateway';
import { MoveGateway } from './dataaccess/moveGateway';

const EMPTY = 0;
const DARK = 1;
const LIGHT = 2;

let INITIAL_BOARD = [
  [EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY],
  [EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY],
  [EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY],
  [EMPTY, EMPTY, EMPTY, DARK, LIGHT, EMPTY, EMPTY, EMPTY],
  [EMPTY, EMPTY, EMPTY, LIGHT, DARK, EMPTY, EMPTY, EMPTY],
  [EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY],
  [EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY],
  [EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY, EMPTY],
];

const PORT = 3000;

const app = express();

app.use(morgan('dev'));
app.use(express.static('static', { extensions: ['html'] }));
app.use(express.json());

const gameGateway = new GameGateway();
const turnGateway = new TurnGateway();
const squareGateway = new SquareGateway();
const moveGateway = new MoveGateway();

app.get('/api/hello', async (req, res) => {
  res.json({
    message: 'Hello Express !!!',
  });
});

app.get('/api/error', async (req, res) => {
  throw new Error('Error endpoint');
});

// ゲームを保存する
app.post('/api/games', async (req, res) => {
  const now = new Date();
  const conn = await connectMySql();

  try {
    await conn.beginTransaction();

    // ゲームを保存
    const gameRecord = await gameGateway.insert(conn, now);

    // ゲームの最初のターンを登録
    const turnRecord = await turnGateway.insert(
      conn,
      gameRecord.id,
      0,
      DARK,
      now
    );

    // 盤面を保存
    await squareGateway.insertAll(conn, turnRecord.id, INITIAL_BOARD);

    // コミット
    await conn.commit();
  } catch {
    await conn.rollback();
    throw new Error();
  } finally {
    await conn.end();
  }

  res.status(201).end();
});

// ターンを取得
app.get('/api/games/latest/turns/:turnCount', async (req, res) => {
  const turnCount = parseInt(req.params.turnCount);
  const conn = await connectMySql();
  try {
    // 最新ゲームを取得
    const gameRecord = await gameGateway.findLatest(conn);
    if (!gameRecord) {
      throw new Error('Latest game not found');
    }

    // 指定したターンを取得
    const turnRecord = await turnGateway.findForGameIdAndTurnCount(
      conn,
      gameRecord.id,
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

    // レスポンスを作成
    const responseBody = {
      turnCount,
      board,
      nextDisc: turnRecord.nextDisc,
      // TODO 決着がついている場合、game_resultsテーブルから取得する
      winnerDisc: null,
    };

    return res.json(responseBody);
  } finally {
    conn.end();
  }
});

// ターンを保存
app.post('/api/games/latest/turns', async (req, res) => {
  const turnCount = parseInt(req.body.turnCount);
  const disc = parseInt(req.body.move.disc);
  const x = parseInt(req.body.move.x);
  const y = parseInt(req.body.move.y);

  const conn = await connectMySql();

  // 最新ゲームを取得
  const gameRecord = await gameGateway.findLatest(conn);
  if (!gameRecord) {
    throw new Error('Latest game not found');
  }

  // 1つ前のターンを取得する
  const previousTurnCount = turnCount - 1;
  const previousTurnRecord = await turnGateway.findForGameIdAndTurnCount(
    conn,
    gameRecord.id,
    previousTurnCount
  );
  if (!previousTurnRecord) {
    throw new Error('Specified turn not found');
  }

  // 盤面を取得
  const squareRecords = await squareGateway.findForTurnId(
    conn,
    previousTurnRecord.id
  );

  // 盤面の値を 8 x 8 の2次元配列にマッピング
  const board = Array.from(Array(8)).map(() => Array.from(Array(8)));
  squareRecords.forEach((s) => {
    board[s.y][s.x] = s.disc;
  });

  // TODO 盤面におけるかチェック

  // 新しい石を置く
  board[y][x] = disc;

  // TODO ひっくり返す

  try {
    await conn.beginTransaction();

    // 次のターンを保存
    const now = new Date();
    const nextDisc = disc === DARK ? LIGHT : DARK;
    const turnRecord = await turnGateway.insert(
      conn,
      gameRecord.id,
      turnCount,
      nextDisc,
      now
    );

    // 盤面を保存
    await squareGateway.insertAll(conn, turnRecord.id, board);

    // movesの登録
    await moveGateway.insert(conn, turnRecord.id, disc, x, y);

    // コミット
    await conn.commit();
  } catch {
    await conn.rollback();
    throw new Error();
  } finally {
    await conn.end();
  }

  res.status(201).end();
});

// MySQLに接続
const connectMySql = async () => {
  return await mysql.createConnection({
    host: 'localhost',
    database: 'reversi',
    user: 'reversi',
    password: 'password',
  });
};

// エラーハンドリング
const errorHandler = (
  err: any,
  _req: express.Request,
  res: express.Response,
  _next: express.NextFunction
) => {
  console.error('Unexpected error occurred', err);
  res.status(500).send({
    message: 'Unexpected error occurred',
  });
};

app.use(errorHandler);

app.listen(PORT, () => {
  console.log(`Reversi application started: http://localhost:${PORT}`);
});
```
