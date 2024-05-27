---
date: <% tp.date.now("YYYY-MM-DD") %>
tags: 
aliases:
  - <% tp.file.title %>
---

## src/schema.graphql

- 新たに feed を拡張する

```ts
"news information"
type Query {
  "The info"
  info: String!
  feed: [Link]!
}

type Link {
  id: ID!
  description: String!
  url: String!
}
```

## src/resolvers.ts

- feed を追加して仮のデータを設定する

```ts
let links = [
  {
    id: 'link-0',
    description: 'GraphQLを学ぶ',
    url: 'https://google.com',
  },
];

export const resolvers = {
  Query: {
    info: () => 'HackerNewsクローン',
    feed: () => links,
  },
};
```