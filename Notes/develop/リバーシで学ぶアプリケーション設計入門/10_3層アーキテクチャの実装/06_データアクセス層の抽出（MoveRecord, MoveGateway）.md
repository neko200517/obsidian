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

## MoveRecord クラス

```bash
touch dataaccess/moveRecord.ts
```

```ts
export class MoveRecord {
  constructor(
    private _id: number,
    private _turnId: number,
    private _disc: number,
    private _x: number,
    private _y: number
  ) {}
}
```

## MoveGateway クラス

```bash
touch dataaccess/moveGateway.ts
```

```ts
import mysql from 'mysql2/promise';
import { MoveRecord } from './moveRecord';

export class MoveGateway {
  // moveの登録
  async insert(
    conn: mysql.Connection,
    turnId: number,
    disc: number,
    x: number,
    y: number
  ) {
    await conn.execute(
      'insert into moves (turn_id, disc, x, y) values (?, ?, ?, ?)',
      [turnId, disc, x, y]
    );
  }
}
```
