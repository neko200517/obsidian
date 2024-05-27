---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
  - リファクタリング
aliases:
  - <% tp.file.title %>
---
## src/resolvers.ts

- resolversに実装した内容を割り当てる

```ts
import { feed } from './resolvers/Query';
import { signup, login, post } from './resolvers/Mutation';
import { postedBy } from './resolvers/Link';
import { links } from './resolvers/User';

export const resolvers = {
  Query: { feed },
  Mutation: { signup, login, post },
  Link: { postedBy },
  User: { links },
};
```