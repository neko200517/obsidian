---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - Prisma
aliases:
  - <% tp.file.title %>
---
## src/schema.graphql

- Mutationに singnup, login を追加
	- signup：ユーザーをサインアップする。戻り値にAuthPayloadを取得する
	- login：ユーザーをログインする。戻り値にAuthPayloadを取得する
- Link に postedBy を追加
	- postedBy：Userテーブルと結合して取得したUser情報
- AuthPayload を追加
	- token：jwtトークン
	- user：User情報
- User を追加
	- links：Linkテーブルと結合して取得したLink情報の配列

```ts
"news information"
type Query {
  "The info"
  info: String!
  feed: [Link]!
}

type Mutation {
  post(url: String!, description: String!): Link!
  signup(email: String!, password: String!, name: String!): AuthPaylaod
  login(email: String!, password: String!): AuthPaylaod
}

type Link {
  id: ID!
  description: String!
  url: String!
  postedBy: User
}

type AuthPaylaod {
  token: String
  user: User
}

type User {
  id: ID!
  name: String
  email: String
  links: [Link!]!
}
```