---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - テスト
aliases:
  - <% tp.file.title %>
---
## findByIdのユニットテストを書く

### src/items/items.service.spec.ts

- 正常系テスト 
- 戻り値resultとexpectedが一致している場合OKになる

```ts
  //...
  describe('findById', () => {
    it('正常系', async () => {
      const expected = {
        id: 'test-id',
        name: 'PC',
        price: 50000,
        description: '',
        status: ItemStatus.ON_SALE,
        createdAt: '',
        updatedAt: '',
        userId: mockUser1.id,
        user: mockUser1,
      };

      jest
        .spyOn(itemRepository, 'findById')
        .mockImplementation(async () => expected);
      const result = await itemsService.findById('tesst-id');

      expect(result).toEqual(expected);
    });
    //...
```

## 異常系テストの追加

- rejects.toThrow 
	- NotFoundException 例外を検証する

```ts
    //...
    it('異常系: 商品が存在しない', async () => {
      jest
        .spyOn(itemRepository, 'findById')
        .mockImplementation(async () => null);
      await expect(itemsService.findById('test-id')).rejects.toThrow(
        NotFoundException,
      );
    });
    //...
```