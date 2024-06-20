---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
  - Prisma
aliases:
  - <% tp.file.title %>
---
## URL

https://www.prisma.io/docs/getting-started

## ライブラリのインストール

```bash
npm install prisma --save-dev
```

## 初期化

```bash
npx prisma init
```

## フォルダ構成

```ts
.
├── prisma
│   └── schema.prisma
└── .env
```

## DB接続情報を編集

### .env

- DBタイプ//ユーザー名:パスワード@ホスト:ポート名/DB名?schema=スキーマ名

```ts
DATABASE_URL="postgresql://udemy_user:udemy_pass@localhost:5432/udemydb?schema=public"
```

## Prismaスキーマファイルの確認
### prisma/schema.prisma

```ts

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```