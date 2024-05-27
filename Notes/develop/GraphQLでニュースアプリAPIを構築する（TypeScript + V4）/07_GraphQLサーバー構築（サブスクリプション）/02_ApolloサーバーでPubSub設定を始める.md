## v4変更点

- ApolloServer単体でSubscriptionをサポートしなくなった
- expressを追加する必要がある

## ライブラリ

```bash
npm i graphql-ws ws @types/ws @graphql-tools/schema graphql-subscriptions cors @types/cors express
```

## PubSub 

Subscriptionを実現するにはPublisher（送信者）とSubscribe（受信者）を決め、双方向に通信することで実現する。

## フォルダ構成

- 新たに追加・更新するファイル

```ts
./
├── src
│   ├── context.ts // update
│   ├── graphql.d.ts
│   ├── index.ts // update
│   ├── resolvers
│   │   ├── Link.ts
│   │   ├── Mutation.ts // update
│   │   ├── Query.ts
│   │   ├── Subscription.ts // add
│   │   └── User.ts
│   ├── resolvers.ts // update
│   ├── schema.graphql // update
│   ├── script.ts
│   └── utils.ts
└── tsconfig.json
```

## scr/index.ts 

- PubSubをContextに組み込み
- makeExecutableSchmaでschemaの作成

```ts
//...
// Subscription
import { makeExecutableSchema } from '@graphql-tools/schema';
import { PubSub } from 'graphql-subscriptions';

const pubsub = new PubSub();
//...

async function startApolloServer() {
  const schema = makeExecutableSchema({ typeDefs, resolvers });
  const server = new ApolloServer({ schema });

  const { url } = await startStandaloneServer(server, {
    context: async ({ req }) => {
      return {
        ...req,
        prisma,
        pubsub,
        userId: req && req.headers.authorization ? getUserId(req) : null,
      };
    },
  });
  //...
}
```

## src/context.ts 

- Contextの型にpubsubを追加

```ts
import { PrismaClient } from '@prisma/client';
import { PubSub } from 'graphql-subscriptions';

export type Context = {
  prisma: PrismaClient;
  pubsub: PubSub;
  userId: number;
};
```

## src/schema.graphql

- Subscriptionにリゾルバを追加

```ts
//...
type Subscripton {
  newLink: Link
}
//...
```

## src/resolvers/Subscption.ts

- ひな型を作成

```ts
import { Context } from '../context';

function newLinkSubscribe(parent: unknown, args: unknown, context: Context) {}
```

## src/resolvers.ts 

- リゾルバを追加

```ts
import { feed } from './resolvers/Query';
import { signup, login, post } from './resolvers/Mutation';
import { postedBy } from './resolvers/Link';
import { links } from './resolvers/User';
import { newLink } from './resolvers/Subscription';

export const resolvers = {
  Query: { feed },
  Mutation: { signup, login, post },
  Subscription: { newLink }, // 追加
  Link: { postedBy },
  User: { links },
};
```