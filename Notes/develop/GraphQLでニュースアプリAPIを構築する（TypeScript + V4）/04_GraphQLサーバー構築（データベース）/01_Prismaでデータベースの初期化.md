---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
  - ORM
aliases:
  - <% tp.file.title %>
---
## QuickStart

https://www.prisma.io/docs/getting-started/quickstart

## インストール

```bash
npm i -D prisma
npm i @prisma/client
```

## 初期化

```bash
npx prisma init
```

以下のファイルが作成されていることを確認する

```
.
├── prisma
│   └── schema.prisma
```