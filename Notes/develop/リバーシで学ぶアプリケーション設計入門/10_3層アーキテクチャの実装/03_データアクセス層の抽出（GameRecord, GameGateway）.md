## dataaccessディレクトリの作成 

```bash
mkdir src/dataaccess 
```

## GameRecordクラス

テーブル構造のクラス

```bash
touch src/dataacess/gameRecord.ts
```

```ts
// src/dataacess/gameRecord.ts 

export class GameRecord {
  constructor(private _id: number, private _startedAt: Date) {}

  get id() {
    return this._id;
  }
}
```

## GameGatewayクラス

テーブルを操作するクラス

```bash
touch src/dataacess/gameGateway.ts
```

```ts
// src/dataacess/gameGateway.ts 

import mysql from 'mysql2/promise';
import { GameRecord } from './gameRecord';

export class GameGateway {
  // 最新ゲームを取得
  async findLatest(conn: mysql.Connection): Promise<GameRecord | undefined> {
    const gameSelectResult = await conn.execute<mysql.RowDataPacket[]>(
      'select id, started_at from games order by id desc limit 1'
    );
    const record = gameSelectResult[0][0];
    if (!record) {
      return undefined;
    }
    return new GameRecord(record['id'], record['started_at']);
  }

  // ゲームを保存
  async insert(conn: mysql.Connection, startedAt: Date): Promise<GameRecord> {
    const gameInsertResult = await conn.execute<mysql.ResultSetHeader>(
      'insert into games (started_at) values (?)',
      [startedAt]
    );
    const gameId = gameInsertResult[0].insertId;
    return new GameRecord(gameId, startedAt);
  }
}
```
