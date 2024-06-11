---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - セキュリティ
aliases:
  - <% tp.file.title %>
---
## ライブラリの導入

```bash
npm i @nestjs/jwt @nestjs/passport passport passport-jwt
npm i -D @types/passport-jwt
npm i @nestjs/config
```

## 環境変数ファイルにシークレットキーを追加 

### .env.local

```ts
SECRET_KEY=secretKey123
```

## ルートモジュールに環境変数を使えるように設定を追加 

### src/app.module.ts

- envFilePath : 
	- .env.localが存在する場合：ファイルから参照する
	- .env.localが存在しない場合：環境変数から参照する（本番環境）
- isGlobal : 有効にすることでどこからでも参照可能にする

```ts
//...
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [
    //...
    ConfigModule.forRoot({
      envFilePath: ['.env.local'],
      isGlobal: true,
    }),
  ],
  //...
})
//...
```

## モジュールにパスワードの設定を追加

- imports に追加 
- PassportModule.register .. : JWTをデフォルトの認証方法として扱う
- JwtModule.register ... : シークレットキーと有効期限の設定
- 環境変数を参照してシークレットキーを設定するため、registerAsyncを使用してConfigServiceをDIする

```ts
//...
import { PassportModule } from '@nestjs/passport';
import { JwtModule } from '@nestjs/jwt';

@Module({
  imports: [
    //...
    PassportModule.register({ defaultStrategy: 'jwt' }),
    JwtModule.registerAsync({
      inject: [ConfigService],
      useFactory: (configService: ConfigService) => ({
        secret: configService.get<string>('SECRET_KEY'),
        signOptions: {
          expiresIn: 3600, // 1時間
        },
      }),
    }),
  ],
  //...
})
//...
```