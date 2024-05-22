---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApploServer
  - Prisma
  - ORM
aliases:
  - <% tp.file.title %>
---
## 参考URL

ApolloServer v4 以上では仕組みが変更になったので以下の対応をする

https://qiita.com/curry__30/items/5b3978f85ae20b0a0716

## src/index.ts

- PrismaClientのインスタンスを作成する
- startStandaloneServer に context を追加して prisma を入れる

```ts
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';
import { readFileSync } from 'fs';
import path from 'path';
import { gql } from 'graphql-tag';
import { resolvers } from './resolvers';
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

const typeDefs = gql(
  readFileSync(path.resolve(__dirname, './schema.graphql'), {
    encoding: 'utf-8',
  })
);

async function startApolloServer() {
  const server = new ApolloServer({ typeDefs, resolvers });

  const { url } = await startStandaloneServer(server, {
    context: async () => ({
      prisma,
    }),
  });
  console.log(`
    Server is runnnig!
    Query at ${url}
  `);
}

startApolloServer();
```

## src/resolvers.ts 

- feed, postの第3引数から contextを呼び出す
- contextの型は定義が必要

```ts
import { PrismaClient } from '@prisma/client';

export type Context = {
  prisma: PrismaClient;
};

export const resolvers = {
  Query: {
    info: () => 'HackerNewsクローン',
    feed: async (parent: unknown, args: unknown, context: Context) => {
      return context.prisma.link.findMany();
    },
  },

  Mutation: {
    post: (
      parent: unknown,
      args: { url: string; description: string },
      context: Context
    ) => {
      const newLink = context.prisma.link.create({
        data: {
          description: args.description,
          url: args.url,
        },
      });

      return newLink;
    },
  },
};
```

## 確認

### クライアントを立ち上げる

```bash
npm run dev
```

### 以下のクエリを実行してデータが永続化されているか確認する

#### Post

```ts
utation Post {
  post(url: "https://yahoo.co.jp", description: "test") {
    id
    description
    url
  }
}
```

#### Feed

```ts
query Feed {
  feed {
    id
    description
    url
  }
}
```