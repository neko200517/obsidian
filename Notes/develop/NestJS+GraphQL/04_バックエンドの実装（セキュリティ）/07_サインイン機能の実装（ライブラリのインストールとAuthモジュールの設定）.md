## フォルダ構成

```ts
├── src
│   ├── auth
│   │   ├── auth.module.ts
│   │   ├── auth.resolver.ts
│   │   ├── auth.service.ts
│   │   ├── dto
│   │   │   ├── signIn.input.ts
│   │   │   └── signInResponse.ts
│   │   ├── guards
│   │   │   └── gql-auth.guard.ts
│   │   ├── strategies
│   │   │   └── local.strategy.ts
│   │   └── types
│   │       └── jwtPayload.type.ts
//...
```

## Auth モジュール、リゾルバ、サービスの作成

```bash
npx nest g module auth
npx nest g resolver auth --no-spec
npx nest g service auth --no-spec
```

## ライブラリのインストール

```bs
npm i @nestjs/passport passport passport-local
npm i -D @types/passport-local
npm i @nestjs/jwt passport-jwt 
npm i -D @types/passport-jwt
```

## シークレットキーの設定

### .env

```ts
//...
JWT_SECRET="jwt@secret#key"
```

## User モジュールをモジュール外に公開する

### src/user/user.module.ts

```ts
import { Module } from '@nestjs/common';
import { UserResolver } from './user.resolver';
import { UserService } from './user.service';
import { PrismaModule } from 'src/prisma/prisma.module';

@Module({
  //...
  exports: [UserService],
})
export class UserModule {}
```

## Auth モジュールにUserModuleとPassportModuleをimport 

### src/auth/auth.module.ts 

- PassportModule.register({ defaultStrategy: 'jwt' })
	- JWTで認証機能を追加する
- JwtModule 
	- secret: process.env.JWT_SECRET,
		- シークレットキーを環境変数から取得
	- signOptions: { expiresIn: '1h' },
		- 有効期限1時間

```ts
import { Module } from '@nestjs/common';
import { AuthResolver } from './auth.resolver';
import { AuthService } from './auth.service';
import { UserModule } from 'src/user/user.module';
import { PassportModule } from '@nestjs/passport';
import { JwtModule } from '@nestjs/jwt';

@Module({
  imports: [
    UserModule,
    PassportModule.register({ defaultStrategy: 'jwt' }),
    JwtModule.register({
      secret: process.env.JWT_SECRET,
      signOptions: { expiresIn: '1h' },
    }),
  ],
  providers: [AuthResolver, AuthService],
})
export class AuthModule {}
```