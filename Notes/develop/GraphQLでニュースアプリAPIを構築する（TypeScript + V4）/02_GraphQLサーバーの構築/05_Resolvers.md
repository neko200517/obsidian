---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApolloServer
aliases:
  - <% tp.file.title %>
---
## src/resolvers.ts

- resolvers.tsファイルを作成する
- Query 内に schema.GraphQL で定義した型のデータを用意する

```ts
export const resolvers = {
  Query: {
    info: () => 'HackerNewsクローン',
  },
};
```

## src/index.ts 

- resolvers を ApolloServer の引数に与える

```ts
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';
import { readFileSync } from 'fs';
import path from 'path';
import { gql } from 'graphql-tag';
import { resolvers } from './resolvers';

const typeDefs = gql(
  readFileSync(path.resolve(__dirname, './schema.graphql'), {
    encoding: 'utf-8',
  })
);

async function startApolloServer() {
  const server = new ApolloServer({ typeDefs, resolvers });

  const { url } = await startStandaloneServer(server);
  console.log(`
    Server is runnnig!
    Query at ${url}
  `);
}

startApolloServer();
```

## 確認する

```bash
npm run dev
```

クライアントにアクセスし、Mock respnses を OFFにして ExampleQuery を実行すると、以下のように表示されることを確認する。

```json
{
  "data": {
    "info": "HackerNewsクローン"
  }
}
```