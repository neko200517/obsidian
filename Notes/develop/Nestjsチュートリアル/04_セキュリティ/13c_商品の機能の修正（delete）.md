---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - セキュリティ
aliases:
  - <% tp.file.title %>
---
## 商品登録ユーザー以外が商品を削除できないようにする

### src/items/items.controller.ts

- ユーザーをデコレータから受け取る
- ユーザーをサービス層に渡す

```ts
  //...
  @Delete(':id')
  @UseGuards(JwtAuthGuard)
  async delete(
    @Param('id', ParseUUIDPipe) id: string,
    @GetUser() user: User,
  ): Promise<void> {
    await this.itemsService.delete(id, user);
  }
  //...
```

### src/items/items.service.ts 

- ユーザーをリポジトリ層に渡す

```ts
  //...
  async delete(id: string, user: User): Promise<void> {
    try {
      return await this.itemRepository.destroy(id, user);
    } catch (e) {
      throw e;
    }
  }
  //...
```

### src/items/item.repository.ts 

- 商品の登録ユーザーと削除しようとするユーザーが異なる場合、例外を出す

```ts
  //...
  async destroy(id: string, user: User): Promise<void> {
    const item = await this.findOneBy({ id });
    if (item.userId !== user.id) {
      throw new BadRequestException('他人の商品を削除することはできません');
    }
    await this.remove(item);
  }
  //...
```