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
      <div class="warnig-message__area">
        <p class="warning-message__text">白の番はスキップです</p>
      </div>
      <div class="board">
        <!-- 8マス -->
        <div class="square">
          <div class="stone dark"></div>
        </div>
        <div class="square">
          <div class="stone light"></div>
        </div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <!-- 8マス -->
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <!-- 8マス -->
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <!-- 8マス -->
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <!-- 8マス -->
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <!-- 8マス -->
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <!-- 8マス -->
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <!-- 8マス -->
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
        <div class="square"></div>
      </div>
      <p>次は黒の番です</p>
    </main>
  </body>
</html>
```

## styles.css

```css
body {
  text-align: center;
}

.warning-message__area {
  height: 30px;
}

.warning-message__text {
  width: 600px;
  height: 30px;
  margin: 0 auto;
  background-color: red;
  color: white;
  font-weight: bold;
  border-radius: 15px;
  line-height: 30px;
}

.board {
  width: 480px;
  margin: 0 auto;
  margin-top: 20px;
  display: grid;
  grid-template-columns: repeat(8, 60px);
  border: solid;
  border-width: 1px 0 0 1px;
}

.square {
  width: 60px;
  height: 60px;
  border: solid;
  border-width: 0 1px 1px 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.stone {
  width: 52px;
  height: 52px;
  border-radius: 50%;
}

.dark {
  background-color: black;
}

.light {
  border: 1px solid;
}
```
