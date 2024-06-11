---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - セキュリティ
aliases:
  - <% tp.file.title %>
---
## 商品と登録したユーザーを紐づける処理の追加

### src/items/items.controller.ts

```ts
//...
export class ItemsController {
  //...
  @Post()
  @UseGuards(JwtAuthGuard)
  async create(
    @Body() createItemDto: CreateItemDto,
    @GetUser() user: User,
  ): Promise<Item> {
    return await this.itemsService.create(createItemDto, user);
  }
  //...
}
```

### src/items/items.service.ts

```ts
//...
@Injectable()
export class ItemsService {
  //...
  async create(createItemDto: CreateItemDto, user: User): Promise<Item> {
    try {
      return await this.itemRepository.createItem(createItemDto, user);
    } catch (e) {
      throw e;
    }
  }
  //...
}
```

### src/items/item.repository.ts 

- create に user オブジェクトを追加

```ts
//...
@Injectable()
export class ItemRepository extends Repository<Item> {
  //...
  async createItem(createItemDto: CreateItemDto, user: User): Promise<Item> {
    const { name, price, description } = createItemDto;
    const item = this.create({
      name,
      price,
      description,
      status: ItemStatus.ON_SALE,
      createdAt: new Date().toISOString(),
      updatedAt: new Date().toISOString(),
      user, // 追加
    });

    await this.save(item);

    return item;
  }
  //...
}
```

## レスポンスにユーザー情報のパスワードが含まれているため除外する

### src/entities/user.entity.ts

- @Entity({ toPlainOnly: true })
	- レスポンスからパスワードの項目を削除する

```ts
//...
import { Exclude } from 'class-transformer';

@Entity('users')
export class User {
  //...
  @Column()
  @Exclude({ toPlainOnly: true }) // レスポンスから外す
  password: string;
  //...
}
```

## Itemコントローラから除外する項目を適用

- @UseInterceptors(ClassSerializerInterceptor)
	- Exclude した項目を除外する

### serc/items/items.controller.ts

```ts
//...
@Controller('items')
@UseInterceptors(ClassSerializerInterceptor)
export class ItemsController {
  //...
```

## 確認

POST /localhost:3000/create した際に user が表示されるが、パスワードが表示されていないこと