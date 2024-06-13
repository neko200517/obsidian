---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## ライブラリのインストール

```bash
npm i @nestjs/graphql @nestjs/apollo @apollo/server graphql
```

## GraphQLを使えるようにする

### src/app.module.ts

- imports に取り込む
- driver: ApolloDriver
	- ApolloServer用のドライバ
- playground: true
	- GUIの管理ツールを使用する
- autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
	- コードファーストで作成する。自動生成されるスキーマファイルはsrc/schema.gqlになる

```ts
import { Module } from '@nestjs/common';
import { GraphQLModule } from '@nestjs/graphql';
import { ApolloDriver, ApolloDriverConfig } from '@nestjs/apollo';
import { join } from 'path';

@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      playground: true,
      autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
    }),
  ],
  providers: [],
})
export class AppModule {}
```

## CORS許可をする

### src/main.ts

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.enableCors();
  await app.listen(3000);
}
bootstrap();
```