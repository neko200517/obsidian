---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - TypeORM
aliases:
  - <% tp.file.title %>
---
## フォルダ構成

```ts
├── src
│   ├── entities
│   │   └── item.entity.ts
```

## Entityの作成

### src/entities/item.entity.ts

- @PrimaryGeneratedColumn('uuid') : uuidで主キーを作成 
- @Column() : 列

```ts
import {
  Column,
  CreateDateColumn,
  Entity,
  PrimaryGeneratedColumn,
  UpdateDateColumn,
} from 'typeorm';
import { ItemStatus } from 'src/items/item-status.enum';

@Entity('items') // テーブル名の変更
export class ItemEntity {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column()
  name: string;

  @Column()
  price: number;

  @Column()
  description: string;

  @Column()
  status: ItemStatus;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}
```