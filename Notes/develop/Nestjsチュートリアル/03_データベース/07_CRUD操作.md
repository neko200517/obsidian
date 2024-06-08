---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - TypeORM
aliases:
  - <% tp.file.title %>
---
## CRUD操作を実装する

- リポジトリパターンを実装する

### src/items/item.repository.ts 

```ts
import { Item } from 'src/entities/item.entity';
import { CreateItemDto } from './dto/create-item.dto';
import { ItemStatus } from './item-status.enum';
import { DataSource, Repository } from 'typeorm';
import { Injectable, NotFoundException } from '@nestjs/common';

@Injectable()
export class ItemRepository extends Repository<Item> {
  constructor(private dataSource: DataSource) {
    super(Item, dataSource.createEntityManager());
  }

  async findAll(): Promise<Item[]> {
    return await this.find();
  }

  async findById(id: string): Promise<Item> {
    return await this.findOneBy({ id });
  }

  async createItem(createItemDto: CreateItemDto): Promise<Item> {
    const { name, price, description } = createItemDto;
    const item = this.create({
      name,
      price,
      description,
      status: ItemStatus.ON_SALE,
      createdAt: new Date().toISOString(),
      updatedAt: new Date().toISOString(),
    });

    await this.save(item);

    return item;
  }

  async updateStatus(id: string): Promise<Item> {
    const item = await this.findOneBy({ id });
    item.status = ItemStatus.SOLD_OUT;
    item.updatedAt = new Date().toISOString();
    return await this.save(item);
  }

  async destroy(id: string): Promise<void> {
    const item = await this.findOneBy({ id });
    await this.remove(item);
  }
}
```

### src/items/item.service.ts

```ts
import { Injectable } from '@nestjs/common';
import { CreateItemDto } from './dto/create-item.dto';
import { ItemRepository } from './item.repository';
import { Item } from 'src/entities/item.entity';
import { InjectRepository } from '@nestjs/typeorm';

@Injectable()
export class ItemsService {
  constructor(
    @InjectRepository(ItemRepository)
    private readonly itemRepository: ItemRepository,
  ) {}

  async findAll(): Promise<Item[]> {
    try {
      return await this.itemRepository.findAll();
    } catch (e) {
      throw new Error(e.message);
    }
  }

  async findById(id: string): Promise<Item> {
    try {
      return await this.itemRepository.findById(id);
    } catch (e) {
      throw new Error(e.message);
    }
  }

  async create(createItemDto: CreateItemDto): Promise<Item> {
    try {
      return await this.itemRepository.createItem(createItemDto);
    } catch (e) {
      throw new Error(e.message);
    }
  }

  async updateStatus(id: string): Promise<Item> {
    try {
      return await this.itemRepository.updateStatus(id);
    } catch (e) {
      throw new Error(e.message);
    }
  }

  async delete(id: string): Promise<void> {
    try {
      return await this.itemRepository.destroy(id);
    } catch (e) {
      throw new Error(e.message);
    }
  }
}
```

### src/items/item.constructor.ts 

```ts
import {
  Body,
  Controller,
  Delete,
  Get,
  Param,
  ParseUUIDPipe,
  Patch,
  Post,
} from '@nestjs/common';
import { ItemsService } from './items.service';
import { Item } from 'src/entities/item.entity';
import { CreateItemDto } from './dto/create-item.dto';

@Controller('items')
export class ItemsController {
  constructor(private readonly itemsService: ItemsService) {}

  @Get()
  async findAll(): Promise<Item[]> {
    return await this.itemsService.findAll();
  }

  @Get(':id') // items/id
  async findById(@Param('id', ParseUUIDPipe) id: string): Promise<Item> {
    return await this.itemsService.findById(id);
  }

  @Post()
  async create(@Body() createItemDto: CreateItemDto): Promise<Item> {
    return await this.itemsService.create(createItemDto);
  }

  @Patch(':id')
  async updateStatus(@Param('id', ParseUUIDPipe) id: string): Promise<Item> {
    return await this.itemsService.updateStatus(id);
  }

  @Delete(':id')
  async delete(@Param('id', ParseUUIDPipe) id: string): Promise<void> {
    await this.itemsService.delete(id);
  }
}
```