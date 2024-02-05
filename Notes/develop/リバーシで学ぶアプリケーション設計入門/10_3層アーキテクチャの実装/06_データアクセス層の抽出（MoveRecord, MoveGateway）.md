## MoveRecordクラス

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

## MoveGatewayクラス

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