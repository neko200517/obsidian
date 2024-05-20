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

## static/index.html に html を実装する

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>リバーシ</title>
  </head>
  <body>
    <header>
      <h1>リバーシで学ぶアプリケーション設計入門</h1>
    </header>
    <main>
      <p>白の番はスキップです</p>
      <div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <!-- 8マス -->
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
        <div>0</div>
      </div>
      <p>次は黒の番です</p>
    </main>
  </body>
</html>
```
