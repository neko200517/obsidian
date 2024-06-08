---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## Controllerとは

- クライアントからリクエストを受け、クライアントにレスポンスを返す
	- Controller がルーティングの機能を担う
- 特定のパスとController が紐づけられる（例：/users と UserController）
- HTTPメソッドとパスを指定したメソッド（ハンドラー）を定義する

## Controller の定義 

1. classに@Controller()デコレータをつける

```ts
import { Controller } from '@nestjs/common';

@Controller('users')
export class UsersController {}
```

1. メソッド（ハンドラー）にHTTPメソッドデコレータをつける

```ts
@Controller('users')
export class UsersController {
  @Post()
  create() {
    // Create User
  }
}
```