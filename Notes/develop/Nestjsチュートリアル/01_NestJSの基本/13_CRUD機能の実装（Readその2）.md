---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## src/items/items.service.ts 

- サービス層にfindByIdメソッドを作成する
- find関数を使用してidが一致するデータを取得する

```ts
//...
export class ItemsService {
  //...
  findById(id: string): Item {
    return this.items.find((item) => item.id === id);
  }
  //...
}
```

## src/items/items.controller.ts

- コントローラ層のfindByIdメソッドを作成する
- @Get(':id') ： /items/id の形式で可変のidを取得（コロン必須）
- @Param('id') ：パスパラメータidを取得

```ts
//...
export class ItemsController {
  //...
  @Get(':id') // items/id
  findById(@Param('id') id: string): Item {
    return this.itemsService.findById(id);
  }
  //...
}
```

## 動作確認

1. item1 を create
2. item2 を create
3. http://localhost/items/item1 で item1 のデータが取得できるかを確認する