---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
  - ORM
aliases:
  - <% tp.file.title %>
---
## prisma/schema.prisma

- provider を sqlite に変更
- url を ./dev.db ファイルに変更
- Link model を定義

```ts
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

// Looking for ways to speed up your queries, or scale easily with your serverless or edge functions?
// Try Prisma Accelerate: https://pris.ly/cli/accelerate-init

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}

model Link {
  id Int @id @default(autoincrement()) // Primary Key
  createdAt DateTime @default(now())
  description String
  url String
}
```

## マイグレーション

```bash
npx prisma migrate dev --name init
```

カレントディレクトリにdev.dbというファイルが作られる

## SQLの確認

以下のSQLが作成される

```sql
-- CreateTable
CREATE TABLE "Link" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "description" TEXT NOT NULL,
    "url" TEXT NOT NULL
);
```