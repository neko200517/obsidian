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

## 良くない例

![[Pasted image 20240201002743.png]]

## 本来のアクティブレコードパターン

- データベースのレコードと対応するデータを持つ
- データアクセスのメソッドを持つ
- ドメインロジックを持つ

![[Pasted image 20240201002856.png]]

ユースケースを分離する方法もあり

![[Pasted image 20240201002912.png]]
