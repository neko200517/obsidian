## StripeRedirect - Schema を作成

- 引数無し

### action/stripe-redirect/schema.ts

```ts
import { z } from 'zod';

export const StripeRedirect = z.object({});
```

## StripeRedirect - Types を作成 

- 戻り値はString型

### action/stripe-redirect/types.ts

```ts
import { z } from 'zod';

import { ActionState } from '@/lib/create-safe-action';

import { StripeRedirect } from './schema';

export type InputType = z.infer<typeof StripeRedirect>;
export type RetrunType = ActionState<InputType, string>;
```

## StripeRedirect - Index を作成 

- orgSubscription 未登録の場合
	- Stripe の決済画面にリダイレクトする
- すでに登録済の場合
	- Stripe の請求管理画面にリダイレクトする

### action/stripe-redirect/index.ts 

```ts
'use server';

import { auth, currentUser } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { StripeRedirect } from './schema';
import { InputType, RetrunType } from './types';
import { absoluteUrl } from '@/lib/utils';
import { stripe } from '@/lib/stripe';

const handler = async (data: InputType): Promise<RetrunType> => {
  const { userId, orgId } = auth();
  const user = await currentUser();

  if (!userId || !orgId || !user) {
    return {
      error: 'Unauthorized',
    };
  }

  const settingsUrl = absoluteUrl(`/organization/${orgId}`);

  let url = '';

  try {
    const orgSubscription = await db.orgSubscription.findUnique({
      where: {
        orgId,
      },
    });

    if (orgSubscription && orgSubscription.stripeCustomerId) {
      const stripeSession = await stripe.billingPortal.sessions.create({
        customer: orgSubscription.stripeCustomerId,
        return_url: settingsUrl,
      });

      url = stripeSession.url;
    } else {
      // 新規登録
      const stripeSession = await stripe.checkout.sessions.create({
        success_url: settingsUrl,
        cancel_url: settingsUrl,
        payment_method_types: ['card'], // 支払方法（カード）
        mode: 'subscription', // 継続
        billing_address_collection: 'auto', // 請求先（auto = 顧客と同じ）
        customer_email: user.emailAddresses[0].emailAddress,
        line_items: [
          {
            price_data: {
              currency: 'JPY', // 通貨
              product_data: {
                name: 'Taskify Pro',
                description: 'Unlimited boards for your organization',
              },
              unit_amount: 2000, // 価格
              recurring: {
                interval: 'month', // 請求期間
              },
            },
            quantity: 1, // 数量
          },
        ],
        metadata: {
          orgId,
        },
      });

      url = stripeSession.url || '';
    }
  } catch (error) {
    return {
      error: 'Something went wrong!',
    };
  }

  revalidatePath(`/organization/${orgId}`);
  return { data: url };
};

export const stripeRedirect = createSafeAction(StripeRedirect, handler);
```