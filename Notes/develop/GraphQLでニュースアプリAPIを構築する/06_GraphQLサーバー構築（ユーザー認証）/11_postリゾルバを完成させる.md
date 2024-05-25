---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
aliases:
  - <% tp.file.title %>
---
## src/context.ts 

- コンテキストの型にuserIdを追加

```ts
import { PrismaClient } from '@prisma/client';

export type Context = {
  prisma: PrismaClient;
  userId: number;
};
```

## src/resolvers/Mutation.ts 

- postedByに { connect: { id: userId } } という形式で割り当てる
- connectに対応したUserテーブルと外部結合する

```ts
// ...
// ニュースを投稿する
export async function post(
  parent: unknown,
  args: { url: string; description: string },
  context: Context
) {
  const { userId } = context;

  return await context.prisma.link.create({
    data: {
      description: args.description,
      url: args.url,
      postedBy: { connect: { id: userId } },
    },
  });
}
```