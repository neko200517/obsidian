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

## nodejs の初期化

```bash
mkdir reversi-app
cd reversi-app
npm init
```

## パッケージのインストール

```bash
npm i -D typescript ts-node
```

## Hello World

```bash
touch hello.ts
```

```ts
// hello.ts

console.log('Hello World');
```

```bash
npx ts-node hello.ts
```
