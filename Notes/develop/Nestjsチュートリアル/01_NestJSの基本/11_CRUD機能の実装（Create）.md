---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
aliases:
  - <% tp.file.title %>
---
## src/items/items.service.ts

- サービス側にcreateメソッドを作成する

```ts
//...
import { Item } from './item.model';

@Injectable()
export class ItemsService {
  //...
  create(item: Item): Item {
    this.items.push(item);
    return item;
  }
}
```

## src/items/items/controller.ts 

- コントローラー側にcreateメソッドを作成する
- @Body引数によりJsonを受けることができる
- それを元にItemインスタンスを作成し、コントローラ側にインスタンスを渡す

```ts
//...
import { Item } from './item.model';
import { ItemStatus } from './item-status.enum';

@Controller('items')
export class ItemsController {
  //...
  @Post()
  create(
    @Body('id') id: string,
    @Body('name') name: string,
    @Body('price') price: number,
    @Body('description') description: string,
  ) {
    const item: Item = {
      id,
      name,
      price,
      description,
      status: ItemStatus.ON_SALE,
    };

    return this.itemsService.create(item);
  }
}
```

## 動作確認

Postmanなどで http://localhost:3000/items にたいして POST メソッドでリクエストを下記のBodyを送信

```json
{
  "id": "test1",
  "name": "PC",
  "price": 50000,
  "description": "とても綺麗です"
}
```

レスポンスとして、パラメータを元に作成された商品のjsonが返ってきたらOK