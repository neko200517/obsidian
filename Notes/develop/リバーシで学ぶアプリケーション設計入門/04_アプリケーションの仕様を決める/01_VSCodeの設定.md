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

## VSCode に Draw.io 拡張機能を導入する

VSCode に Draw.io integration 拡張機能を導入する。

## 設定

```bash
mkdir .vscode
touch .vscode/settings.json
```

### ./vscode/settings.json

```json
{
  // 背景色を変更する
  "hediet.vscode-drawio.theme": "Kennedy"
}
```

## ドキュメントを作成

```bash
mkdir doc
touch doc/conetxt-diagram.drawio
```
