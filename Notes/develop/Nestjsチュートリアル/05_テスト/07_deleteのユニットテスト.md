---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - テスト
aliases:
  - <% tp.file.title %>
---
## deleteのユニットテストを書く

### src/items/items.service.spec.ts

- 登録itemと削除者idが異なる場合
	- →異常系へ
- 登録itemと削除者idが一致している場合 
	- →正常系へ
　
```ts
    //...
    describe('delete', () => {
    const mockItem = {
      id: 'test-id',
      name: 'PC',
      price: 50000,
      description: '',
      status: ItemStatus.SOLD_OUT,
      createdAt: '',
      updatedAt: '',
      userId: mockUser1.id,
      user: mockUser1,
    };
    it('正常系', async () => {
      jest
        .spyOn(itemRepository, 'destroy')
        .mockImplementation(async () => mockItem);
      await expect(itemsService.delete('test-id', mockUser1)).resolves.toEqual(
        undefined,
      );
    });

    it('異常系: 他人の商品を削除', async () => {
      jest
        .spyOn(itemRepository, 'destroy')
        .mockImplementation(async () => null);
      await expect(itemsService.delete('test-id', mockUser2)).rejects.toThrow(
        BadRequestException,
      );
    });
    //...
```