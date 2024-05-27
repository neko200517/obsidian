---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
  - ORM
aliases:
  - <% tp.file.title %>
---
## クライアントの生成

```bash
npx prisma generate
```

## src/script.ts

- src/script.tsを作成
- サンプルデータを一件挿入し結果を表示するといった内容を記述する

```ts
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

async function main() {
  const newLink = await prisma.link.create({
    data: {
      description: 'GraphQLを学ぶ',
      url: 'https://google.com',
    },
  });

  const allLinks = await prisma.link.findMany();
  console.log(allLinks);
}

main()
  .catch((e) => {
    throw e;
  })
  .finally(async () => {
    // データベースを閉じる
    prisma.$disconnect;
  });
```

## 実行

```bash
npx ts-node ./src/script.ts
```

このような結果が返ってきたらOK

```json
[
  {
    id: 1,
    createdAt: 2024-05-22T06:21:26.765Z,
    description: 'GraphQLを学ぶ',
    url: 'https://google.com'
  }
]
```