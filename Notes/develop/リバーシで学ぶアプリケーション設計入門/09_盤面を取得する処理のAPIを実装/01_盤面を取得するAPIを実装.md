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

## 盤面を取得する API

/api/games/latest/turns/{turnCount}にアクセスする API を作成する。
前提：MySQL に接続する関数を作成してモジュール化した。

1. テーブルから最新のゲーム情報を取得
2. 最新のゲームと指定した turnCount のターンを取得
3. ターンの盤面を取得
4. 取得した盤面を 8 x 8 の 2 次元配列にマッピングして json 形式でレスポンスを返す

```ts
// main.ts

// 中略

//　盤面を取得
app.get('/api/games/latest/turns/:turnCount', async (req, res) => {
  const turnCount = parseInt(req.params.turnCount);
  const conn = await connectMySql();
  try {
    // 最新ゲームを取得
    const gameSelectResult = await conn.execute<mysql.RowDataPacket[]>(
      'select id, started_at from games order by id desc limit 1'
    );
    const game = gameSelectResult[0][0];

    // 指定したターンを取得
    const tunrnSelectResult = await conn.execute<mysql.RowDataPacket[]>(
      'select id, game_id, turn_count, next_disc, end_at from turns where game_id = ? and turn_count = ?',
      [game['id'], turnCount]
    );
    const turn = tunrnSelectResult[0][0];

    // 盤面を取得
    const squareSelectResult = await conn.execute<mysql.RowDataPacket[]>(
      'select id, turn_id, x, y, disc from squares where turn_id = ? order by id',
      [turn['id']]
    );

    // 盤面の値を 8 x 8 の2次元配列にマッピング
    const squares = squareSelectResult[0];
    const board = Array.from(Array(8)).map(() => Array.from(Array(8)));
    squares.forEach((s) => {
      board[s.y][s.x] = s.disc;
    });

    // レスポンスを作成
    const responseBody = {
      turnCount,
      board,
      nextDisc: turn['next_disc'],
      // TODO 決着がついている場合、game_resultsテーブルから取得する
      winnerDisc: null,
    };

    return res.json(responseBody);
  } finally {
    conn.end();
  }
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

// 中略
```
