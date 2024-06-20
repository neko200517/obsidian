## PassportStrategyを継承したJwtStrategyを作成する

### src/auth/strategies/jwt.strategy.ts 

- PassportStrategyを継承し、引数にpassprt-jwtのStrategyを与える
- jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
	- AuthorizationヘッダにBearerとトークンを付与する認証方式
- ignoreExpiration: false,
	- 期限切れだと認証を通さないようにする
- secretOrKey: process.env.JWT_SECRET,
	- 環境変数のシークレットキーを指定
- valicate
	- 認証が通ったらユーザーを返す

```ts
import { Injectable } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { UserService } from 'src/user/user.service';
import { JwtPayload } from '../types/jwtPayload.type';
import { User } from '@prisma/client';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private readonly userService: UserService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: process.env.JWT_SECRET,
    });
  }

  async validate(payload: JwtPayload): Promise<User | null> {
    return await this.userService.getUser(payload.email);
  }
}
```

## AuthGuardを継承したJwtAuthGuardを作成 

### src/auth/guards/jwt-auth.guard.ts 

- REST用のためGraphQL用にオーバーライド

```ts
import { ExecutionContext } from '@nestjs/common';
import { GqlExecutionContext } from '@nestjs/graphql';
import { AuthGuard } from '@nestjs/passport';

export class JwtAuthGuard extends AuthGuard('jwt') {
  getRequest(context: ExecutionContext) {
    const ctx = GqlExecutionContext.create(context);
    return ctx.getContext().req;
  }
}
```

## PlayGroundで確認

### サインイン

```
mutation signIn($signInInput: SignInInput!) {
  signIn(signInInput: $signInInput) {
    accessToken
  }
}
```

### accessToken をコピー

```ts
{
  "data": {
    "signIn": {
      "accessToken": "xxxxxx",
    }
  }
}
```

### getUserを実行

```ts
query getUser($email: String!) {
  getUser(email: $email) {
    id,
    name,
    email,
    createdAt,
    updatedAt
  }
}
```

### HTTP HEADERS

- コピーしたaccessTokenを貼り付けて実行

```ts
{
  "Authorization": "Bearer xxxxxx"
}
```

Userのレスポンスが返ってきたらOK 
トークンをあえて間違えてエラーが返ってきたらOK