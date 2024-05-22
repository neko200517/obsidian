---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApploServer
aliases:
  - <% tp.file.title %>
---
## Getting Start

https://www.apollographql.com/tutorials/intro-typescript

## setup

- Apollo Server (TypeScript) のテンプレートを clone

```bash
git clone https://github.com/apollographql-education/intro-typescript.git
```

## src/schema.graphql

- スキーマを定義

```ts
"news information"
type Query {
  "The info"
  info: String! // Not Null
}
```