---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## src/items/items.service.ts 

- サービス層にdeleteメソッドを作成する
- filter関数を使用してデータを削除する

```ts
//...
export class ItemsService {
  //...
  delete(id: string): void {
    this.items = this.items.filter((item) => item.id !== id);
  }
  //...
}
```

## src/items/items.controller.ts

- コントローラ層のdeleteメソッドを作成する
- @Deleteメソッドで実装する

```ts
//...
export class ItemsController {
  //...
  @Delete(':id')
  delete(@Param('id') id: string): void {
    this.itemsService.delete(id);
  }
  //...
}
```

## 動作確認

1. item1 を create
2. DELETE http://localhost/items/item1 で item1 が削除されていることを確認する