---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## URL

https://docs.nestjs.com/

## NestJSとは

- Node.js上で動作するオープンソースのバックエンド開発フレームワーク
- TypeScriptで作られている
- Expressをコアにして作られている
- Angularにインスパイアされている

## NestJSを使うメリット

- 型の恩恵を得ることができる
- Expressの機能やライブラリを使うことができる
- Nest CLIをつかってプロジェクトやファイルのテンプレートを生成できる
- テストフレームワークが標準で用意されている
- 拡張性が高い
	- データベース（RDB、NoSQL）、セキュリティ、GraphQL、WebSocket、etc...

## NestJSを使うデメリット

- 公式ドキュメントが日本語に未対応
- RoR、Laravel、Djangoなどと比較すると利用ユーザーが少なく、まだまだ情報が少ない

## NestJS CLI 

- Nestコマンドラインインタフェース
	- プロジェクトの作成、サービスの作成など