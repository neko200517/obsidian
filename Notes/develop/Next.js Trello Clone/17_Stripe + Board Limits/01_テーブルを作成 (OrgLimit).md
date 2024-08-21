## OrgLimit テーブルを作成

### prisma/schema.prisma

```ts
//...

model OrgLimit {
  id              String @id @default(uuid())
  orgId           String @unique @map("org_id")
  count           Int @default(0)

  createdAt       DateTime @default(now()) @map("created_at")
  updatedAt       DateTime @updatedAt @map("updated_at")

  @@map("org_limit")
}
```

## DBをリセット

```bash
npx prisma generate reset
npx prisma generate
npx prisma db push
```