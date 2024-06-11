---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - テスト
aliases:
  - <% tp.file.title %>
---
## updatedStateのユニットテストを書く

### src/items/items.service.spec.ts

- 登録itemと購入者idが異なる場合
	- →正常系へ
- 登録itemと購入者idが一致している場合 
	- →異常系へ
　
```ts
  //...
  describe('updateStatus', () => {
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
        .spyOn(itemRepository, 'updateStatus')
        .mockImplementation(async () => mockItem);
      await expect(
        itemsService.updateStatus('test-id', mockUser2),
      ).resolves.toEqual(mockItem);
    });

    it('異常系: 自身の商品を購入', async () => {
      jest
        .spyOn(itemRepository, 'updateStatus')
        .mockImplementation(async () => null);
      await expect(
        itemsService.updateStatus('test-id', mockUser1),
      ).rejects.toThrow(BadRequestException);
    });
  });
  //...
```