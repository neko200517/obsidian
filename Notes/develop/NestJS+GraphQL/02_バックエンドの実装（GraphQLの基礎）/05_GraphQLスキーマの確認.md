---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## src/task/models/task.model.ts

```ts
import { Field, Int, ObjectType } from '@nestjs/graphql';

@ObjectType()
export class Task {
  @Field(() => Int)
  id: number;

  @Field()
  name: string;

  @Field()
  dueDate: string;

  @Field()
  status: 'NOT_STARTED' | 'IN_PROGRESS' | 'COMPLETED';

  @Field({ nullable: true })
  description: string;
}
```

↓

## src/schema.gqp

```ts
# ------------------------------------------------------
# THIS FILE WAS AUTOMATICALLY GENERATED (DO NOT MODIFY)
# ------------------------------------------------------

type Task {
  id: Int!
  name: String!
  dueDate: String!
  status: String!
  description: String
}

type Query {
  getTasks: [Task]!
}
```

## その他のオプション

- name
	- フィールド名
- description 
	- コメント
- defaultValue
	- 初期値

### モデル

```ts
@Field({
    nullable: true,
    name: 'desc',
    description: 'タスクの説明',
    defaultValue: 'default',
  })
  description: string;
```

↓

### GraphQLスキーマ

```ts
  """タスクの説明"""
  desc: String
```