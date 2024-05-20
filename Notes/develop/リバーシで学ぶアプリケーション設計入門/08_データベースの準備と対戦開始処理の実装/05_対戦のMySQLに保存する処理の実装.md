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

## 必要なパッケージのインストール

```bash
npm i mysql2
```

## 登録処理の実装

```ts
// src/main.ts
import mysql from 'mysql2/promise';

// 中略

// ゲームを保存する
app.post('/api/games', async (req, res) => {
  const startedAt = new Date();

  const conn = await mysql.createConnection({
    host: 'localhost',
    database: 'reversi',
    user: 'reversi',
    password: 'password',
  });

  try {
    await conn.beginTransaction();
    await conn.execute('insert into games (started_at) values (?)', [
      startedAt,
    ]);
    await conn.commit();
  } finally {
    await conn.end();
  }

  res.status(201).end();
});

// 中略
```
