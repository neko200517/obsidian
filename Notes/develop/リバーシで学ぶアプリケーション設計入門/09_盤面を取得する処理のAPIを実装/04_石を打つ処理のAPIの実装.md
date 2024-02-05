```ts

// 中略

app.use(express.json()); // jsonを受け取るための記述

// 中略

// ターンを保存
app.post('/api/games/latest/turns', async (req, res) => {
  const turnCount = parseInt(req.body.turnCount);
  const disc = parseInt(req.body.move.disc);
  const x = parseInt(req.body.move.x);
  const y = parseInt(req.body.move.y);

  const conn = await connectMySql();

  // 最新ゲームを取得
  const gameSelectResult = await conn.execute<mysql.RowDataPacket[]>(
    'select id, started_at from games order by id desc limit 1'
  );
  const game = gameSelectResult[0][0];

  // 1つ前のターンを取得する
  const previousTurnCount = turnCount - 1;
  const tunrnSelectResult = await conn.execute<mysql.RowDataPacket[]>(
    'select id, game_id, turn_count, next_disc, end_at from turns where game_id = ? and turn_count = ?',
    [game['id'], previousTurnCount]
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

  // 新しい石を置く
  board[y][x] = disc;

  try {
    await conn.beginTransaction();

    // 次のターンを保存
    const now = new Date();
    const nextDisc = disc === DARK ? LIGHT : DARK;
    const turnnInsertResult = await conn.execute<mysql.ResultSetHeader>(
      'insert into turns (game_id, turn_count, next_disc, end_at) values (?, ?, ?, ?)',
      [game['id'], turnCount, nextDisc, now]
    );

    // 次のターンIDを取得
    const turnId = turnnInsertResult[0].insertId;

    // マス目の数をカウント
    const squareCount = board
      .map((line) => line.length)
      .reduce((v1, v2) => v1 + v2, 0);

    // ①64マスの現在の状態を保存するSQL
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

    // movesの登録
    await conn.execute(
      'insert into moves (turn_id, disc, x, y) values (?, ?, ?, ?)',
      [turnId, disc, x, y]
    );

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

// 中略

```