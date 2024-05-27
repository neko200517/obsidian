---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApolloServer
aliases:
  - <% tp.file.title %>
---
## QueryとMutation

CRUD操作における役割

Query : GET
Muitation: POST, PUT, DELETE

## src/shema.graphql

- Mutation を追加
- post を追加
- 戻り値に Link

```ts
// ...
type Mutation {
  post(url: String!, description: String!): Link!
}
// ...
```

## src/resolvers.ts

- resolvers に post メソッドを追加する。戻り値として追加したデータを返す

```ts
let links = [
  {
    id: 'link-0',
    description: 'GraphQLを学ぶ',
    url: 'https://google.com',
  },
];

export const resolvers = {
  // ...

  Mutation: {
    post: (parent: any, args: { url: string; description: string }) => {
      let idCount = links.length;

      const link = {
        id: `link-${idCount++}`,
        description: args.description,
        url: args.url,
      };

      links.push(link);
      return link;
    },
  },
};
```