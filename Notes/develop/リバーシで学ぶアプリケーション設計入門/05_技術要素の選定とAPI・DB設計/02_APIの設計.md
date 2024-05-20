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

## API の設計

```bash
touch webapi-design-memo.md
```

機能一覧の機能をもとにそれぞれの API を設計する。
画面イメージや概念モデルも参考に漏れがないかを練っていく。

## 対戦を開始する

「対戦」を登録する

POST /api/games

## 現在の盤面を表示する

指定したターン数の「ターン」を取得する

GET /api/games/latest/turns/{turnCount}

レスポンスボディ

```json
{
  "turnCount": 1, // ターン数
  "board": [
    // 盤面 8 x 8
    [0, 0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 1, 2, 0, 0, 0],
    [0, 0, 0, 2, 1, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0]
  ],
  "nextDisc": 1, // 次の石...0:空, 1:黒, 2:白
  "winnerDisc": 1 // 勝利している石...null:勝敗無し, 1:黒, 2:白
}
```

## 石を打つ

「ターン」を登録する

POST /api/games/latest/turns

リクエストボディ

```json
{
  "turnCount": 1, // ターン数
  "move": {
    // 移動座標
    "disc": 1, // 石の種類
    "x": 0, // x座標
    "y": 0 // y座標
  }
}
```

## 自分の対戦結果を表示する

「対戦」の一覧を取得する

GET /api/games

レスポンスボディ

```json
{
  "games": [
    {
      "id": 1,
      "winnerDisc": 1,
      "startedAt": "YYYY-MM-DD hh:mm:ss" // 対戦開始時刻
    },
    {
      "id": 2,
      "winnerDisc": 1,
      "startedAt": "YYYY-MM-DD hh:mm:ss"
    }
  ]
}
```
