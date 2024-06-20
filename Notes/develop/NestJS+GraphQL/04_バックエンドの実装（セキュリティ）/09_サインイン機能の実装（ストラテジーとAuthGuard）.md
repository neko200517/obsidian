## URL

https://docs.nestjs.com/graphql/other-features
https://zenn.dev/mseto/articles/nest-graphql-prisma

## Stragegyとは

- OAuthやEmail & パスワード方式などの認証方式を決めるもの

## PassportStrategyを継承したLocalStrategyを作成

### src/auth/strategies/local.strategy.ts

- PassportStrategyを継承し、引数に passport-local の Strategy を渡す
- super({ usernameField: 'email' });
	- デフォルトではusernameとpasswordで認証するようになっているので、コンストラクタをオーバーライドしてemailを使うように変更する
- validataをオーバーライドして成功したらUserを返す

```ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { Strategy } from 'passport-local';
import { AuthService } from '../auth.service';
import { User } from '@prisma/client';

@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private readonly authService: AuthService) {
    super({ usernameField: 'email' });
  }

  async validate(email: string, password: string): Promise<User> {
    const user = await this.authService.valicateUser(email, password);
    if (!user) {
      throw new UnauthorizedException();
    }
    return user;
  }
}
```

## AuthモジュールにLocalStrategyを入れる

### src/auth/auth.module.ts

```ts
//...
@Module({
  //...
  providers: [AuthResolver, AuthService, LocalStrategy],
})
export class AuthModule {}
```

## AuthGuardとは

- ResolverやControllerなどの各メソッドに付与してStrategyで許可されたものしか処理を続行させないようにする機能

## AuthGuardを継承したGalAuthGuardを作成する

### src/auth/guards/gql-auth.guard.ts

- getRequest 
	- REST用なのでGraphQL用にするためにコンストラクタとgetRequestメソッドをオーバーライドする
- GqlExecutionContext.create(context);
	- 実行コンテキストの作成 
- ctx.getContext();
	- コンテキストの取得
- ctx.getArgs().SignInInput;
	- コンテキストの中からSignInInputを取得。これはリゾルバの@Args('signInInput')と同じものであるため、名前を合わせておくこと

```ts
import { ExecutionContext } from '@nestjs/common';
import { GqlExecutionContext } from '@nestjs/graphql';
import { AuthGuard } from '@nestjs/passport';

export class GqlAuthGuard extends AuthGuard('local') {
  constructor() {
    super();
  }

  getRequest(context: ExecutionContext) {
    const ctx = GqlExecutionContext.create(context);
    const request = ctx.getContext();
    request.body = ctx.getArgs().signInInput;
    return request;
  }
}
```