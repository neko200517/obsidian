---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApolloServer
aliases:
  - <% tp.file.title %>
---
## src/index.ts

- schema.graphql を読み込む
- スタンドアロンサーバーを立ち上げる

```ts
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';
import { readFileSync } from 'fs';
import path from 'path';
import { gql } from 'graphql-tag';

const typeDefs = gql(
  readFileSync(path.resolve(__dirname, './schema.graphql'), {
    encoding: 'utf-8',
  })
);

async function startApolloServer() {
  const server = new ApolloServer({ typeDefs });
  const { url } = await startStandaloneServer(server);
  console.log(`
    Server is runnnig!
    Query at ${url}
  `);
}

startApolloServer();
```

## 起動

```bash
npm run dev
```