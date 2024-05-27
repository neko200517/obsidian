---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
  - ORM
  - PrismaStudio
aliases:
  - <% tp.file.title %>
---
## PrismaStudioの起動

以下のコマンドでDBクライアントが起動する。データのCRUD操作が可能なので簡易的にDBをメンテナンス可能。

```bash
npx prisma studio
```

