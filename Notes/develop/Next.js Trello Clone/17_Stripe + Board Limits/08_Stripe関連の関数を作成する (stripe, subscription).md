## ライブラリのインストール

```bash
npm i stripe
```

## stripeインスタンスを作成

### lib/stripe.ts

```ts
import Stripe from 'stripe';

export const stripe = new Stripe(process.env.STRIPE_API_KEY!, {
  apiVersion: '2024-06-20',
  typescript: true,
});
```

## subscription関数を作成 

- orgSubscriptionテーブルを検索し、stripeのIdと有効期限を検証する

### lib/subscription.ts 

```ts
import { auth } from '@clerk/nextjs/server';

import { db } from '@/lib/db';

const DAY_IN_MS = 86_400_000; // 1day

export const checkSubscription = async () => {
  const { orgId } = auth();

  if (!orgId) {
    return false;
  }

  const orgSubscription = await db.orgSubscription.findUnique({
    where: {
      orgId,
    },
    select: {
      stripeSubscriptionId: true,
      stripeCurrentPeriodEnd: true,
      stripeCustomerId: true,
      stripePriceId: true,
    },
  });

  if (!orgSubscription) {
    return false;
  }

  const isValid =
    orgSubscription.stripeSubscriptionId &&
    orgSubscription.stripeCurrentPeriodEnd?.getTime()! + DAY_IN_MS > Date.now();

  // isValid は "" と undefiled が返る可能性があるためboolean型として保証するため !! を使用
  return !!isValid;
};
```

## utilsに関数を追加 

- リダイレクト先の絶対パスを取得する

### lib/utils.ts

```ts
import { type ClassValue, clsx } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// 追加
export function absoluteUrl(path: string) {
  return `${process.env.NEXT_PUBLIC_APP_URL}${path}`;
}
```