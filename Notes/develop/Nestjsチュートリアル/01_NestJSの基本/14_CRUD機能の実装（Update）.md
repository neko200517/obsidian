---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## src/items/items.service.ts 

- サービス層にupdateStatusメソッドを作成する
- findById関数を使用してidが一致するデータを取得する
- statusをSOLD_OUTに変更する（itemは参照なので配列のデータを上書きする必要なし）

```ts
//...
export class ItemsService {
  //...
  updateStatus(id: string): Item {
    const item = this.findById(id);
    item.status = ItemStatus.SOLD_OUT;
    return item;
  }
  //...
}
```

## src/items/items.controller.ts

- コントローラ層のupdateStatusメソッドを作成する
- @Patchメソッドで実装する

```ts
//...
export class ItemsController {
  //...
  @Patch(':id')
  updateStatus(@Param('id') id: string): Item {
    return this.itemsService.updateStatus(id);
  }
  //...
}
```

## 動作確認

1. item1 を create
2. PATCH http://localhost/items/item1 で item1 のstatusがSOLD_OUTになっていることを確認する