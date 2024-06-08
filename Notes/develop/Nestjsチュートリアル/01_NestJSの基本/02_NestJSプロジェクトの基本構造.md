---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## フォルダ構成

```ts
.
├── README.md
├── nest-cli.json
├── package-lock.json
├── package.json
├── src
│   ├── app.controller.spec.ts // テストに利用される
│   ├── app.controller.ts      // コントローラー
│   ├── app.module.ts　　　　　　// モジュール
│   ├── app.service.ts         // サービス
│   └── main.ts                // エントリポイント
├── test                       // テストファイル
│   ├── app.e2e-spec.ts
│   └── jest-e2e.json
├── tsconfig.build.json
└── tsconfig.json
```

## 不要なファイルを削除

後で作成するので以下のファイルを削除する

```bash
rm src/app.controller.spec.ts 
rm src/app.controller.ts 
rm src/app.service.ts
```

## src/app.mocule.ts 

参照を削除

```ts
import { Module } from '@nestjs/common';

@Module({
  imports: [],
  controllers: [],
  providers: [],
})
export class AppModule {}
```