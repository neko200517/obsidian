---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
aliases:
  - <% tp.file.title %>
---
## src/index.ts

- startStandaloneServerに追加。リクエストを受け取れるように修正
- reqオブジェクトに追加する形となるためスプレッド演算子で記述する
- リクエストが存在した時のみgetUserIdを実行する

```ts
// ...
async function startApolloServer() {
  // ...
  const { url } = await startStandaloneServer(server, {
    context: async ({ req }) => {
      return {
        ...req,
        prisma,
        userId: req && req.headers.authorization ? getUserId(req) : null,
      };
    },
  });
  // ...
}
```
