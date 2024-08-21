## OrgSubscription テーブルを作成する

- strripe～フィールドの型はオプショナルで定義

### prisma/schema.prisma

```ts
model OrgSubscription {
  id                      String  @id @default(uuid())
  orgId                   String  @unique @map("org_id")

  stripeCustomerId        String?   @unique @map("stripe_customer_id")
  stripeSubscriptionId    String?   @unique @map("stripe_subscription_id")
  stripePriceId           String?   @map("stripe_price_id")
  stripeCurrentPeriodEnd  DateTime? @map("stripe_current_period_end")

  @@map("org_subscription")
}
```

## マイグレーション

```bash
npx prisma generate
npx prisma db push
```