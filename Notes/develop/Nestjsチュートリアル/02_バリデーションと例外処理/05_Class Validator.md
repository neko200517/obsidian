---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## URL

https://github.com/typestack/class-validator
https://github.com/typestack/class-transformer

## ライブラリを導入

```bash
npm i -D class-validator class-transformer
```

## DTOクラスのプロパティにバリデーションを追加

### バリデーション一覧

https://github.com/typestack/class-validator?tab=readme-ov-file#validation-decorators

### src/dto/create-item.dto.ts

- name : 文字列、空白以外、最大40文字
- number : 数値型、最小1、文字列を数値型にキャスト
- description : 文字列、空白以外

```ts
import { Type } from 'class-transformer';
import { IsString, IsNotEmpty, MaxLength, IsInt, Min } from 'class-validator';

export class CreateItemDto {
  @IsString()
  @IsNotEmpty()
  @MaxLength(40)
  name: string;

  @IsInt()
  @Min(1)
  @Type(() => Number)
  price: number;

  @IsString()
  @IsNotEmpty()
  description: string;
}
```

## グローバルバリデーションを適用

### src/main.ts 

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe()); // 追加
  await app.listen(3000);
}
bootstrap();
```