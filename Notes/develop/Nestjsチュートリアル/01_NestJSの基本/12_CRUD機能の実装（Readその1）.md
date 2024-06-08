---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## src/items/items.service.ts

- サービス層にfindAllメソッドを作成する

```ts
//...
export class ItemsService {
  //...
  findAll(): Item[] {
    return this.items;
  }
}
```

## src/items/items.controller.ts 

- コントローラ層にfindAllメソッドを作成する
- サービス層のメソッドを呼び出す

```ts
//...
export class ItemsController {
  //...
  @Get()
  findAll(): Item[] {
    return this.itemsService.findAll();
  }
  //...
}
```

## 動作確認

1. item1 を create
2. item2 を create
3. http://localhost/items で全てのデータが取得できるかを確認する