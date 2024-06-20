---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
  - Prisma
aliases:
  - <% tp.file.title %>
---
## NestJSにPrismaクライアントを使用する

https://docs.nestjs.com/recipes/prisma#use-prisma-client-in-your-nestjs-services

## ライブラリのインストール

```bash
npm i @prisma/client
```

## モジュールとサービスを作成

```bash
npx nest g module prisma 
npm nest g service prisma
```

## フォルダ構成

```ts
.
├── src
│   ├── prisma
│   │   ├── prisma.module.ts // 追加
│   │   └── prisma.service.ts // 追加
//...
```

## Prismaサービスの実装

### src/prisma/prisma.service.ts 

```ts
import { Injectable, OnModuleInit } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit {
  async onModuleInit() {
    await this.$connect();
  }
}
```

## Prismaサービスを exports に登録

### src/prisma/prisma.module.ts

- exports
	- Prismaモジュール外でも使用可能にする

```ts
import { Module } from '@nestjs/common';
import { PrismaService } from './prisma.service';

@Module({
  providers: [PrismaService],
  exports: [PrismaService],
})
export class PrismaModule {}
```

## なぜクライアントをセットアップするのか

- Prismaスキーマで定義した型をプログラムから利用するため自動的に型を生成する
- node_modules/.prisma/client/index.d.ts にモデル、型の定義が追加される