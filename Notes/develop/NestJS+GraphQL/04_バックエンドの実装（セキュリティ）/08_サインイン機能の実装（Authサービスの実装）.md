## サインイン入力クラスの作成

### src/auth/dto/signIn.input.ts

```ts
import { Field, InputType } from '@nestjs/graphql';
import { IsEmail, MinLength } from 'class-validator';

@InputType()
export class SignInInput {
  @Field()
  @IsEmail()
  email: string;

  @Field()
  @MinLength(8)
  password: string;
}
```

## サインインレスポンスクラスの作成

### src/auth/dto/signInResponse.ts

```ts
import { Field, ObjectType } from '@nestjs/graphql';
import { User } from 'src/user/models/user.model';

@ObjectType()
export class SignInResponse {
  @Field()
  accessToken: string;

  @Field(() => User)
  user: User;
}
```

## ユーザー検証サービスの追加

### src/auth/auth.service.ts

- emailからユーザーを取得
- ユーザーのパスワードと入力されたパスワードを複合、比較して同じならUserを返し、それ以外の場合 null を返す

```ts
import { Injectable } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { User } from '@prisma/client';
import { UserService } from 'src/user/user.service';
import * as bcrypt from 'bcrypt';
import { SignInResponse } from './dto/signInResponse';
import { JwtPayload } from './types/jwtPayload.type';

@Injectable()
export class AuthService {
  constructor(
    private readonly userService: UserService,
    private readonly jwtService: JwtService,
  ) {}

  async valicateUser(email: string, password: string): Promise<User | null> {
    const user = await this.userService.getUser(email);
    if (user && (await bcrypt.compare(password, user.password))) {
      return user;
    }
    return null;
  }
}
```

## JWTペイロードクラスの作成 

### src/auth/types/jwtPayload.type.ts

```ts
export type JwtPayload = {
  email: string;
  sub: number;
};
```

## サインインサービスの追加 

### src/auth/auth.service.ts 

- 引数の User は validateUser から取得したユーザーを想定している
- this.jwtService.sign(payload)
	- emai, user.id を元にアクセストークンを取得

```ts
//...
  async signIn(user: User): Promise<SignInResponse> {
    const payload: JwtPayload = { email: user.email, sub: user.id };
    return { accessToken: this.jwtService.sign(payload), user };
  }
//...
```