## Auth リゾルバの実装

### src/auth/auth.resolver.ts

- @Context() context: any
	- コンテキストの取得
- this.authService.signIn(context.user);
	- コンテキストには認証の通った（LocalStoragy.validateの結果）User が含まれている

```ts
import { Args, Context, Mutation, Resolver } from '@nestjs/graphql';
import { AuthService } from './auth.service';
import { SignInResponse } from './dto/signInResponse';
import { UseGuards } from '@nestjs/common';
import { GqlAuthGuard } from './guards/gql-auth.guard';
import { SignInInput } from './dto/signIn.input';

@Resolver()
export class AuthResolver {
  constructor(private readonly authService: AuthService) {}

  @Mutation(() => SignInResponse)
  @UseGuards(GqlAuthGuard)
  async signIn(
    @Args('signInInput') signInInput: SignInInput,
    @Context() context: any,
  ) {
    return await this.authService.signIn(context.user);
  }
}
```

## サインイン機能の追加で登録されたGraphQLスキーマの確認

### src/schema.gql

```ts
//...
type SignInResponse {
  accessToken: String!
  user: User!
}
//...
type Mutation {
  //...
  signIn(signInInput: SignInInput!): SignInResponse!
}
//...
input SignInInput {
  email: String!
  password: String!
}
```

## PlayGroundで動作確認

### リクエスト

```ts
mutation signIn($signInInput: SignInInput!) {
  signIn(signInInput: $signInInput) {
    accessToken
    user {
      id
      name
      email
    }
  }
}
```

### QUERY VARIABLES

```ts
{
  "signInInput": {
    "email": "user1@gmail.com",
    "password": "password"
  }
}
```

### レスポンス

```ts
{
  "data": {
    "signIn": {
      "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InVzZXIxQGdtYWlsLmNvbSIsInN1YiI6MSwiaWF0IjoxNzE4NTUxNzE4LCJleHAiOjE3MTg1NTUzMTh9.tQ2mMsHhmd3kY40KVwCcFuJSMhR7MxFDxLgP5Cn-R_o",
      "user": {
        "id": 1,
        "name": "user1",
        "email": "user1@gmail.com"
      }
    }
  }
}
```