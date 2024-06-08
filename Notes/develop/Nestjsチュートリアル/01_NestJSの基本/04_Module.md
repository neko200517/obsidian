---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## Moduleとは

- 関連するControllerやServiceなどをまとめ、アプリケーションとして利用できるようにNestJSに登録する役割
- NestJSアプリケーションには必ず1つ以上のルートモジュールと、0個以上のFeatureモジュールが必要となる

## Moduleの定義

1. classに @Module デコレータをつける
2. @Module()デコレータにのプロパティを記述する

```ts
@Module({
  imports: [UserModule],
  controllers: [AppController],
  providres: [AppService],
  exports: []
})
export class AppModule {}
```

## @Moduleデコレータのプロパティ 

- imports: モジュール内部で必要な外部モジュールを記述
- controllers: @Controllerデコレータがついたクラスを記述
- providers: @Injectableデコレータがついたクラスを記述 
- exports: 外部のモジュールにエクスポートしたいものを記述

## サンプル

### app.module.ts

ルートモジュール。UserModuleをインポートしている

```ts
@Module({
  imports: [UserModule],
  controllers: [AppController],
  providres: [AppService],
  exports: []
})
export class AppModule {}
```

### users.module.ts 

```ts
@Module({
  imports: [],
  controllers: [UserController],
  providres: [UsersService],
  exports: [UsersService]
})
export class UserModule {}
```