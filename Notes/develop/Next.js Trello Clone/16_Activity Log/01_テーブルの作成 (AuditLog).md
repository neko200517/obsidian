## 新しく AuditLog テーブルを作成する

### prisma/schema.prisma

```ts
//...

enum ACTION {
  CREATE
  UPDATE
  DELETE
}

enum ENTITY_TYPE {
  BOARD
  LIST
  CARD
}

model AuditLog {
  id             String @id @default(uuid())
  orgId          String @map("org_id")
  action         ACTION
  entityId       String @map("entity_id")
  entityType     ENTITY_TYPE @map("entity_type")
  entityTitle    String @map("entity_title")
  userId         String @map("user_id")
  userImage      String @db.Text @map("user_image")
  userName       String @db.Text @map("user_name")

  createdAt      DateTime @default(now()) @map("created_at")
  updatedAt      DateTime @updatedAt @map("updated_at")

  @@map("audit_log")
}
```

## DB の反映

```bash
npx prisma db push
```

## Prisma/Client の生成

```bash
npx prisma generate
```
