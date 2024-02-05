## パッケージをインストール

```bash
npm i express
npm i -D @types/express
```

## ファイルを作成

```bash
mkdir src
touch src/main.ts
```

## Hello World

```ts
// src/main.ts

import express from 'express';

const PORT = 3000;

const app = express();

app.get('/api/hello', async (req, res) => {
  res.json({
    message: 'Hello Express',
  });
});

app.listen(PORT, () => {
  console.log(`Reversi application started: http://localhost:${PORT}`);
});
```

## 試しに起動

```bash
npx ts-node .\src\main.ts
```

http://localhost:3000/api/hello にアクセスし、

```json
{"message": "Hello World"}
```

が返ってきたらOK
