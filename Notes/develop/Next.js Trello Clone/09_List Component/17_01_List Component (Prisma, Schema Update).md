## ファイル構成

```ts
.
├── prisma
│   └── schema.prisma
//...
└── types.ts
```

## Prisma Schema の更新

- List と Card の追加
- @relation でテーブル同士のリレーションを構築する
	- Board と List は 1 対 n
	- List と Card は 1対n
	- 双方とも onDelete: Cascade で親を削除すると子を削除する
	- 子側に外部キーとrelationを組むテーブルの関係性を定義する
	- 親側に子を配列（1対nの場合）として定義する
- @@Index で外部キーをIndex化

### prisma/schema.prisma

```ts
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

// Looking for ways to speed up your queries, or scale easily with your serverless or edge functions?
// Try Prisma Accelerate: https://pris.ly/cli/accelerate-init

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Board {
  id            String @id @default(uuid())
  orgId         String @map("org_id")
  title         String
  imageId       String @map("image_id")
  imageThumbUrl String @db.Text @map("image_thumb_url")
  imageFullUrl  String @db.Text @map("image_full_url")
  imageUserName String @db.Text @map("image_user_name")
  imageLinkHtml String @db.Text @map("image_link_html")

  lists         List[]

  createdAt     DateTime @default(now()) @map("created_at")
  updatedAt     DateTime @updatedAt @map("updated_at")

  @@map("boards")
}

model List {
  id            String @id @default(uuid())
  title         String
  order         Int

  boardId       String @map("board_id")
  board         Board @relation(fields: [boardId], references: [id], onDelete: Cascade)

  cards         Card[]

  createdAt     DateTime @default(now()) @map("created_at")
  updatedAt     DateTime @updatedAt @map("updated_at")

  @@index([boardId])
  @@map("lists")
}

model Card  {
  id            String @id @default(uuid())
  title         String
  order         Int
  description   String? @db.Text

  listId        String @map("list_id")
  list          List @relation(fields: [listId], references: [id], onDelete: Cascade)

  createdAt     DateTime @default(now()) @map("created_at")
  updatedAt     DateTime @updatedAt @map("updated_at")

  @@index([listId])
  @@map("cards")
}
```

## DB を更新する

- npm run dev している場合は Ctrl + C で中断しておくこと

```bash
npx prisma migrate reset
npx prisma db push
npx prisma generate
```

## 結合後の型を定義する

- 結合後の型は @prisma/client から取得できないため作成する

### types.ts

```tsx
import { Card, List } from '@prisma/client';

export type ListWithCards = List & { cards: Card[] };

export type CardWithList = Card & { list: List };
```
