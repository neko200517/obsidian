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

## 既存のファイルを移動

static ディレクトリに game フォルダを作成し、以前作成した html, js, css を移動。
トップページ用に新しい html を作成。

```bash
./static
│  index.html # ← 新規作成
│  style.css
│  main.js
│
└─game
        index.html # ← 今までのファイル
        main.js
        style.css
```

今までの html は http://localhost:3000/game でアクセスすることができる。

## static/index.html

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="style.css" />
    <title>リバーシ</title>
  </head>
  <body>
    <header>
      <h1>リバーシで学ぶアプリケーション設計入門</h1>
    </header>
    <main>
      <a href="/game/">対戦する</a>
      <table>
        <thead>
          <tr>
            <th>黒を打った回数</th>
            <th>白を打った回数</th>
            <th>勝った石</th>
            <th>対戦開始時刻</th>
            <th>対戦終了時刻</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>10</td>
            <td>10</td>
            <td>黒</td>
            <td>2024-01-01 00:00:00</td>
            <td>2024-01-01 00:00:00</td>
          </tr>
          <tr>
            <td>10</td>
            <td>10</td>
            <td>黒</td>
            <td>2024-01-01 00:00:00</td>
            <td>2024-01-01 00:00:00</td>
          </tr>
        </tbody>
      </table>
    </main>
    <script src="main.js"></script>
  </body>
</html>
```

## static/style.css

```css
body {
  text-align: center;
}

table {
  padding-top: 50px;
  margin: 0 auto;
}
```
