---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - テスト
aliases:
  - <% tp.file.title %>
---
## createのユニットテストを書く

### src/items/items.service.spec.ts

- 登録した item と expected が一致すればテストOK

```ts
  //...
  describe('create', () => {
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

      const item = {
        name: 'PC',
        price: 50000,
        description: '',
      };

      jest
        .spyOn(itemRepository, 'createItem')
        .mockImplementation(async () => expected);
      const result = await itemsService.create(item, mockUser1);

      expect(result).toEqual(expected);
    });
  });
  //...
```
