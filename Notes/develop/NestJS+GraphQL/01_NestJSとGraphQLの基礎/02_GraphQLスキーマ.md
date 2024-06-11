---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## GraphQLスキーマとは

- GraphQL  APIが受け付けるクエリの内容が定義された仕様書
	- GraphQL Schema Languageという独自の言語で記述される
	- 独自の言語を使用することで、特定の言語やフレームワークに依存しない
- 拡張子には .graphqls や .graphql, .gql などが使われる
- さまざまな型を扱うことができる
- 実際にデータを扱うには「リゾルバ」を実装する必要がある

## GraphQLスキーマの例

```ts
type Task {
  id: Int!
  name: String!
  dueDate: String!
  status: String!
  description: String!
}

type Query {
  getTasks: [Task!]!
}
```

## オブジェクト型

- 1つ以上のフィールドからなる集合の型
- クライアントから取得可能なデータの構造
- オブジェクト型をネストさせることも可能 

```ts
type Object {
  field1: Type 
  field2: Type! // ! をつけることで NotNULL にする
  ...
}
```

## スカラー型

- 単一のフィールドに付ける型
- GraphQL では5つのスカラー型が存在
	- Int : 整数型
	- Float : 浮動小数点型
	- String : 文字列型
	- Boolean : 論理型
	- ID : ユニークな文字列型

## リスト型

- 単一のフィールドに付ける型
- フィールドが複数の値のリストからなる場合に使用される
- ! の位置によってNot NULLのパターンが異なる

```ts
type User {
  tasks1: [Task]!  // リスト自体は必ず存在するが、null要素を持つ可能性がある
  tasks2: [Task!]  // リスト自体はnullの可能性があるが、null要素を持つことはない
  tasks3: [Task!]! // リスト自体は必ず存在し、null要素も持たない
  tasks4: [Task]   // リスト自体はnullの可能性があり、null要素を含む可能性がある
}
```

### 例）

```ts
field: [String]! の場合
↓
field: null // error
field: [] // ok
field: ['a', 'b'] // ok
field: ['a', null, 'b'] // ok 
```

```ts
field: [String!] の場合
↓
field: null // ok
field: [] // ok
field: ['a', 'b'] // ok
field: ['a', null, 'b'] // error
```

## 列挙型

- あらかじめ列挙された値のみ取ることができる
- 単一のフィールドの型として使用する

```ts
enum Color {
  RED
  BLUE
  GREEN
}
```

## ユニオン型

- 複数の型のうちいずれかの型であることを表す

```ts
union Vehicle = Car | Bike
```

## Query型

- GraphQL APIが提供するQueryのエントリーポイントを定義 

```ts
type Query {
  getTasks: [Task!]!
  getTask(id: Int!): Task
}
```

## Mutation型

- GraphQL APIが提供するMutationのエントリーポイントを定義 

```ts
type Mutation {
  createTask(id: Int!, name: String!): Task!
  deleteTask(id: Int!): Task!
}
```