---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## Serviceとは

- ビジネスロジックを定義する
- Controllerから呼び出すことでユースケースを実現する
	- Controllerにビジネスロジックを書いてもプログラムは動作するが、責務を分割することで補修性、拡張性が上がりよい設計となる

## Dependency Injection (DI)

- 日本語にすると「依存性の注入」
- 依存関係のあるオブジェクトを外部から渡す 
- DIの用途例：
	- 本番用とテスト用でインスタンスの切り替え 
	- ログの出力先の切り替え
- DIコンテナ：
	- DIを容易に行える仕組みのこと
	- NestJSなど多くのフレームワークにに備わっている

## Serviceの定義 

1. classに@Injectable()デコレータをつける。※@Service()ではないので注意

```ts
import { Injectalbe } from '@nestjs/common';

@Injectable()
export class UsersService {}
```

1. ビジネスロジックを実現するメソッドを作成する

```ts
@Injectable()
export class UsersService {
  find(userName: number) {
    // find User
  }
}
```

## ControllerからServiceを利用する

1. ModuleのprovidersにServiceを登録する

```ts
@Module({
  controllers: [UsersController],
  providers: [UserService]
})
export class UserModule {}
```

1. ControllerのconstructorでServiceを引数にとる

```ts
@controller('usres')
export class UsersController {
  constructor(private readonly userService: UsersService) {}

　　＠Get(':username')
　　find(@Param('username') userName: string) {
　　  this.userService.find(userName);
　　}
}
```