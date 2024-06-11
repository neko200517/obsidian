---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - セキュリティ
aliases:
  - <% tp.file.title %>
---
## 商品登録ユーザーが商品の状態を更新できないようにする

### src/items/items.controller.ts

- ユーザーをデコレータから受け取る
- ユーザーをサービス層に渡す

```ts
  //...
  @Patch(':id')
  @UseGuards(JwtAuthGuard)
  async updateStatus(
    @Param('id', ParseUUIDPipe) id: string,
    @GetUser() user: User,
  ): Promise<Item> {
    return await this.itemsService.updateStatus(id, user);
  }
  //...
```

### src/items/items.service.ts 

- ユーザーをリポジトリ層に渡す

```ts
  //...
  async updateStatus(id: string, user: User): Promise<Item> {
    try {
      return await this.itemRepository.updateStatus(id, user);
    } catch (e) {
      throw e;
    }
  }
  //...
```

### src/items/item.repository.ts 

- 商品の登録ユーザーとupdateStatusのユーザーが同じ場合、例外を出す

```ts
  //...
  async updateStatus(id: string, user: User): Promise<Item> {
    const item = await this.findOneBy({ id });
    if (item.userId === user.id) {
      throw new BadRequestException('自身の商品を購入することはできません');
    }
    item.status = ItemStatus.SOLD_OUT;
    item.updatedAt = new Date().toISOString();
    return await this.save(item);
  }
  //...
```