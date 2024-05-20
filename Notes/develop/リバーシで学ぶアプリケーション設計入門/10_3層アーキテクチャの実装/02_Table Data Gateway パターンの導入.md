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

## Table Data Gateway パターン

### データアクセス層の実装には以下のようなパターンがある

- Table Data Gateway
- Repository
- Active Record

### Table Data Gateway パターン

テーブルと 1 対 1 対応するクラスを作成し、対象のテーブルとのやりとりを記述する Table Data Gateway パターンを実装する。
Table Data Gateway パターンは Java 界隈では「DAO（Data Access Object）パターン」と呼ばれることがある。
