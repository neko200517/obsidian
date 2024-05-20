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

## SquareRecored クラス

```bash
touch dataaccess/squareRecord.ts
```

```ts
export class SquareRecord {
  constructor(
    private _id: number,
    private _turnId: number,
    private _x: number,
    private _y: number,
    private _disc: number
  ) {}

  get x() {
    return this._x;
  }

  get y() {
    return this._y;
  }

  get disc() {
    return this._disc;
  }
}
```

## SquareGateway クラス

```bash
touch dataaccess/squareGateway.ts
```

```ts
import mysql from 'mysql2/promise';
import { SquareRecord } from './SquareRecord';

export class SquareGateway {
  // 指定したターンを取得
  async findForTurnId(
    conn: mysql.Connection,
    turnId: number
  ): Promise<SquareRecord[]> {
    // 盤面を取得
    const record = await conn.execute<mysql.RowDataPacket[]>(
      'select id, turn_id, x, y, disc from squares where turn_id = ? order by id',
      [turnId]
    );

    const squares = record[0].map(
      (r) => new SquareRecord(r['id'], r['turn_id'], r['x'], r['y'], r['disc'])
    );

    return squares;
  }

  // ゲームの最初のターンを登録
  async insertAll(conn: mysql.Connection, turnId: number, board: number[][]) {
    const squareCount = board
      .map((line) => line.length)
      .reduce((v1, v2) => v1 + v2, 0);

    const squaresInsertSql =
      'insert into squares (turn_id, x, y, disc) values ' +
      Array.from(Array(squareCount))
        .map(() => '(?, ?, ?, ?)')
        .join(', ');

    // ②squareInsertSqlの(?, ?, ?, ?)に入る一次元配列を作成
    const squareInsertValues: any[] = [];
    board.forEach((line, y) => {
      line.forEach((disc, x) => {
        squareInsertValues.push(turnId);
        squareInsertValues.push(x);
        squareInsertValues.push(y);
        squareInsertValues.push(disc);
      });
    });

    // ①と②を併せてSQLを実行
    await conn.execute(squaresInsertSql, squareInsertValues);
  }
}
```
