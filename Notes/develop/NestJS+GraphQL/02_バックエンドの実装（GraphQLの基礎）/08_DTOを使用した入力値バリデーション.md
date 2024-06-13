---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## バリデーションについて

https://github.com/typestack/class-validator#validation-decorators

## ライブラリのインストール

```bash
npm i class-validator class-transformer
```

## アプリにバリデーション機能を追加する

### src/main.ts

- app.useGlobalPipes(new ValidationPipe());
	- パイプ機能でバリデーション機能をアプリ全体に追加

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.enableCors();
  app.useGlobalPipes(new ValidationPipe()); // 追加
  await app.listen(3000);
}
bootstrap();
```

## フォルダ構成

```ts
├── src
│   ├── app.module.ts
│   ├── main.ts
│   ├── schema.gql
│   └── task
│       ├── dto
│       │   └── task.dto.ts // 追加
│       ├── models
│       │   └── task.model.ts
│       ├── task.module.ts
│       ├── task.resolver.ts
│       └── task.service.ts
```

## DTOクラスの作成

### src/task/dto/task.dto.ts

- @InputType 
	- GraphQLの入力タイプを指定
- @IsNotEmpty 
	- NOT NULL にする
- @IsDateString 
	- 日付形式のString型を指定

```ts
import { Field, InputType } from '@nestjs/graphql';
import { IsDateString, IsNotEmpty } from 'class-validator';

@InputType()
export class CreateTaskInput {
  @Field()
  @IsNotEmpty() // 追加
  name: string;

  @Field()
  @IsDateString() // 追加
  dueDate: string;

  @Field({ nullable: true })
  description?: string;
}
```

## リゾルバを修正

### src/task/task.resolver.ts

- createTask(@Args('createTaskInput') createTaskInput: CreateTaskInput)
	- CreateTask型で引数を受け取る

```ts
//...
  @Mutation(() => Task)
  createTask(@Args('createTaskInput') createTaskInput: CreateTaskInput): Task {
    return this.taskService.createTask(createTaskInput);
  }
//...
```

## サービスを修正 

### src/task/task.service.ts

```ts
//...
  createTask(createTaskInput: CreateTaskInput): Task {
    const { name, dueDate, description } = createTaskInput;
    const newTask = new Task();
    newTask.id = this.tasks.length + 1;
    newTask.name = name;
    newTask.dueDate = dueDate;
    newTask.description = description;
    newTask.status = 'NOT_STARTED';
    this.tasks.push(newTask);

    return newTask;
  }
}
//...
```

## 生成されたGraphQL

### src/schema.gql

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

type Mutation {
  createTask(createTaskInput: CreateTaskInput!): Task!
}

input CreateTaskInput {
  name: String!
  dueDate: String!
  description: String
}
```

## バリデーションの動作確認

- name
	- "" 空文字でエラー。それ以外はOK
- dueDate
	- "test" 日付型以外エラー。それ以外はOK

### リクエスト

```ts
mutation createTask($createTaskInput: CreateTaskInput!) {
  createTask(createTaskInput: $createTaskInput) {
    id
    name
    dueDate
    description
    status
  }
}
```

### QUERY VARIABLES

```ts
{
  "createTaskInput": {
    "name": "test task",
    "dueDate": "2024-01-01"
  }
}
```

### レスポンス

```ts
{
  "data": {
    "createTask": {
      "id": 2,
      "name": "test task",
      "dueDate": "2024-01-01",
      "description": null,
      "status": "NOT_STARTED"
    }
  }
}
```