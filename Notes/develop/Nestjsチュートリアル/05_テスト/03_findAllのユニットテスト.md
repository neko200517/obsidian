---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - テスト
aliases:
  - <% tp.file.title %>
---
## findAllのユニットテストを書く

### src/items/items.service.spec.ts

- jest.spyOn
	- itemRepository に findAll メソッドを登録する
- mockImplemantion(async () => expected)
	- 関数を実行した結果、戻り値にexpectedを返す
- const result = await itemsService.findAll();
	- resultに空の配列がセットされる
- expect(result).toEqual(expected)
	- 空の配列と空の配列を比較して同じだったらテストOKとなる

```ts
  //...
  describe('findAll', () => {
    it('正常系', async () => {
      const expected: Item[] = [];
      jest
        .spyOn(itemRepository, 'findAll')
        .mockImplementation(async () => expected);
      const result = await itemsService.findAll();
      expect(result).toEqual(expected);
    });
  });
  //...
```