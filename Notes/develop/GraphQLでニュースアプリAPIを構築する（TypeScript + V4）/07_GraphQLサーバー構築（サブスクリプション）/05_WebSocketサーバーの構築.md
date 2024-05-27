---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
  - Subscription
aliases:
  - <% tp.file.title %>
---
## src/index.ts

- expressサーバーとApolloServerを統合する
- エンドポイントは localhost:4000/graphql になる
- useServer でコンテキストを共有しないとコンテキストの値を渡すことができないので注意

```ts
import { ApolloServer } from '@apollo/server';
import { readFileSync } from 'fs';
import path from 'path';
import { gql } from 'graphql-tag';
import { resolvers } from './resolvers';
import { PrismaClient } from '@prisma/client';
import { getUserId } from './utils';

// Subscription
import bodyParser from 'body-parser';
import cors from 'cors';
import express from 'express';
import { expressMiddleware } from '@apollo/server/express4';
import { createServer } from 'http';
import { ApolloServerPluginDrainHttpServer } from '@apollo/server/plugin/drainHttpServer';
import { makeExecutableSchema } from '@graphql-tools/schema';
import { WebSocketServer } from 'ws';
import { useServer } from 'graphql-ws/lib/use/ws';
import { PubSub } from 'graphql-subscriptions';
import { Context } from './context';

const PORT = 4000;
const pubsub = new PubSub();

const prisma = new PrismaClient();

// Schema Definition
const typeDefs = gql(
  readFileSync(path.resolve(__dirname, './schema.graphql'), {
    encoding: 'utf-8',
  })
);

// Create Schema
const schema = makeExecutableSchema({ typeDefs, resolvers });

// Set up Http Server
const app = express();
const httpServer = createServer(app);

// Set up WebSocket Server
const wsServer = new WebSocketServer({
  server: httpServer,
  path: '/graphql',
});
const serverCleanup = useServer(
  {
    schema,
    context: async () => ({
      prisma,
      pubsub,
    }),
  },
  wsServer
);

// Set up ApolloServer
const server = new ApolloServer<Context>({
  schema,
  plugins: [
    ApolloServerPluginDrainHttpServer({ httpServer }),
    {
      async serverWillStart() {
        return {
          async drainServer() {
            await serverCleanup.dispose();
          },
        };
      },
    },
  ],
});

(async () => {
  try {
    await server.start();

    app.use(
      '/graphql',
      cors<cors.CorsRequest>(),
      bodyParser.json(),
      expressMiddleware(server, {
        context: async ({ req }) => ({
          ...req,
          prisma,
          pubsub,
          userId: req && req.headers.authorization ? getUserId(req) : undefined,
        }),
      })
    );

    httpServer.listen(PORT, () => {
      console.log(
        `🚀 Query endpoint ready at http://localhost:${PORT}/graphql`
      );
      console.log(
        `🚀 Subscription endpoint ready at ws://localhost:${PORT}/graphql`
      );
    });
  } catch (error) {
    console.error('Error starting server: ', error);
  }
})();

```