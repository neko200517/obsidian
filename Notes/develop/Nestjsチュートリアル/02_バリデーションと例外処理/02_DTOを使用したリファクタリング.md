---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## フォルダ構成

dtoフォルダと dto モデルを作成する

```ts
├── src
│   ├── app.module.ts
│   ├── items
│   │   ├── dto
│   │   │   └── create-item.dto.ts // 追加
│   │   ├── item-status.enum.ts
│   │   ├── item.model.ts
│   │   ├── items.controller.ts
│   │   ├── items.module.ts
│   │   └── items.service.ts
│   └── main.ts
```

## src/items/dto/create-item.dto.ts 

- ここではcreateメソッドが引数を受け取るときの型を定義する

```ts
export class CreateItemDto {
  id: string;
  name: string;
  price: number;
  description: string;
}
```

## src/items/items.controller.ts

- コントローラ層の create メソッドをリファクタリングする
- 引数の型を CreateItemDto に変更する

```ts
//...
import { CreateItemDto } from './dto/create-item.dto';
//...
export class ItemsController {
  //...
  @Post()
  create(@Body() createItemDto: CreateItemDto) {
    return this.itemsService.create(createItemDto);
  }
  //...
}
```

## src/items/items.service.ts

- サービス層の create メソッドをリファクタリングする
- 受け取った createItemDto を Item 型に変換する

```ts
//...
import { CreateItemDto } from './dto/create-item.dto';
//...
export class ItemsService {
  //...
  create(createItemDto: CreateItemDto): Item {
    const item: Item = {
      ...createItemDto,
      status: ItemStatus.ON_SALE,
    };
    this.items.push(item);
    return item;
  }
  //...
}
```