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

## API の実装

```ts
// src/main.ts

// 中略

// ゲームを保存する
app.post('/api/games', async (req, res) => {
  const startedAt = new Date();
  console.log(startedAt);

  res.status(201).end();
});

// 中略
```

## クライアントの実装

```js
// static/main.js

// 中略

// ゲームの登録
const registerGame = async () => {
  await fetch('/api/games', {
    method: 'POST',
  });
};

const main = async () => {
  await registerGame(); // 追加
  await showBoard();
};

main();
```
