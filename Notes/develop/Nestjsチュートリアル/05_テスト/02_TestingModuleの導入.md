---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - テスト
aliases:
  - <% tp.file.title %>
---
## TestingModuleの導入

### src/items/items.service.spec.ts 

- Testモジュールのインポート
- providers に Service, Repository の登録

```ts
import { Test } from '@nestjs/testing';
import { ItemsService } from './items.service';
import { ItemRepository } from './item.repository';

// Repositoryのメソッド一覧
const mockItemRepository = () => ({
  findAll: jest.fn(),
  findById: jest.fn(),
  createItem: jest.fn(),
  updateStatus: jest.fn(),
  destroy: jest.fn(),
});

describe('ItemsServiceTest', () => {
  let itemsService;
  let itemRepository;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        ItemsService,
        {
          provide: ItemRepository,
          useFactory: mockItemRepository,
        },
      ],
    }).compile();

    itemsService = module.get<ItemsService>(itemsService);
    itemRepository = module.get<ItemRepository>(itemRepository);
  });

  // ここにテストを書く
  describe('test', () => {
    //...
  });
});
```

## import文の修正

import文のパス指定を絶対パス→相対パスに変更する

'src/...' -> '../...'

例）

```ts
import { User } from 'src/entities/user.entity';
```

↓

```ts
import { User } from '.../entities/user.entity';
```