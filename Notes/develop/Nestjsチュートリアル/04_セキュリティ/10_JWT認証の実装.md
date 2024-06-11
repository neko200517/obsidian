---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - セキュリティ
aliases:
  - <% tp.file.title %>
---
## URL

https://docs.nestjs.com/recipes/passport

## フォルダ構成

```ts
│   ├── auth
│   │   ├── auth.controller.ts
│   │   ├── auth.module.ts
│   │   ├── auth.service.ts
│   │   ├── dto
│   │   │   ├── create-user.dto.ts
│   │   │   └── credentials.dto.ts
│   │   ├── guards
│   │   │   └── jwt-auth.guard.ts // 追加
│   │   ├── jwt.strategy.ts // 追加
│   │   ├── user-status.enum.ts
│   │   └── user.repository.ts
```

## JWTストラテジーの実装

### src/auth/jwt.strategy.ts

- PassportStrategyにJWTのStrategyを与えるとでJWT用のStrategyになる
- jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken :
	-  ヘッダのAuthorazationプロパティのBearerトークンで認証する
- ignoreExpiration: false
	- 有効期限が来たら認証が切れるようにする
- secretOrKey 
	- シークレットキーを渡す
- validateの関数名は固定なので変えないようにする
- id, usenameから user を検索してユーザーが存在すれば認証が通るようにする

```ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { UserRepository } from './user.repository';
import { User } from 'src/entities/user.entity';
import { ConfigService } from '@nestjs/config';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(
    private userRepository: UserRepository,
    private configService: ConfigService,
  ) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: configService.get<string>('SECRET_KEY'),
    });
  }

  async validate(payload: { id: string; username: string }): Promise<User> {
    const { id, username } = payload;
    const user = await this.userRepository.findOneBy({ id, username });

    if (user) {
      return user;
    }
    throw new UnauthorizedException();
  }
}
```

## Guardを作成する

認可を通す役割を果たす

### src/auth/guards/guard.ts

```ts
import { Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}
```

## 商品のCRUD操作に認可を加える

- ガードはハンドラ単位、モジュール単位、コントローラー単位など細かく制御可能
- @UseGuards(JwtAuthGuard) : 
	- 商品コントローラーの create, updateStatus, delete にガードをかける
	- 引数にJwtAuthGuardを渡すことでJwt認証に通ったものだけが操作可能になる

```ts
import {
  UseGuards,
} from '@nestjs/common';
import { JwtAuthGuard } from 'src/auth/guards/jwt-auth.guard';

@Controller('items')
export class ItemsController {
  //...
  @Post()
  @UseGuards(JwtAuthGuard)
  async create(@Body() createItemDto: CreateItemDto): Promise<Item> {
    //...
  }

  @Patch(':id')
  @UseGuards(JwtAuthGuard)
  async updateStatus(@Param('id', ParseUUIDPipe) id: string): Promise<Item> {
    //...
  }

  @Delete(':id')
  @UseGuards(JwtAuthGuard)
  async delete(@Param('id', ParseUUIDPipe) id: string): Promise<void> {
    //...
  }
}
```

## 確認

1. POST /localhost:300/auth/signin で取得したアクセストークンをコピーする
2. POST /loalhost:3000/item/ でcreateする
	1. Headerの Authorazation に Bearer + コピーしたアクセストークンを貼り付ける
	2. create できればOK
	3. トークンを間違えて create できなければOK