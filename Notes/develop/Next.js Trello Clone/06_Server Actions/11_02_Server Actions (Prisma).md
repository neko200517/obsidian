## ファイル構成

```ts
.
├── lib
│   └── db.ts
├── prisma
│   └── schema.prisma
```

## Board テーブルの定義を追加

### prisma/schema.prisma

```ts
//...

model Board {
  id String @id @default(uuid())
  title String
}
```

## Prisma クライアントのインストール

```bash
npm i @prisma/client
```

## Prisma クライアントの更新

```bash
npx prisma generate
```

## DBのpush

```bash
npx prisma db push
```

## Board テーブルが Neon にデプロイされていることを確認する

左メニューから Tables を確認する

![[Pasted image 20240729165738.png]]

## Prisma Client のセットアップ

### Next.js で Prisma Client をインスタンス化するためのベスト プラクティス

https://www.prisma.io/docs/orm/more/help-and-troubleshooting/help-articles/nextjs-prisma-client-dev-practices

### lib/db.ts

```ts
import { PrismaClient } from '@prisma/client';

declare global {
  var prisma: PrismaClient | undefined;
}

export const db = globalThis.prisma || new PrismaClient();

if (process.env.NODE_ENV !== 'production') globalThis.prisma = db;
```