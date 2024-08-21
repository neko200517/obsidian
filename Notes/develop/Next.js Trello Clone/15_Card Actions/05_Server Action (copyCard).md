## ファイル構成

```ts
.
├── actions
│   └── copy-card
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## Schema を作成する

### actions/copy-card/schema.ts

```ts
import { z } from 'zod';

export const CopyCard = z.object({
  id: z.string(),
  boardId: z.string(),
});
```

## Types を作成する

### actions/copy-card/types.ts

```ts
import { z } from 'zod';
import { Card } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { CopyCard } from './schema';

export type InputType = z.infer<typeof CopyCard>;
export type RetrunType = ActionState<InputType, Card>;
```

## Index を作成する

 - コピーするデータの order は最大値 + 1

### actions/copy-card/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { CopyCard } from './schema';
import { InputType, RetrunType } from './types';

const handler = async (data: InputType): Promise<RetrunType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { id, boardId } = data;
  let card;

  try {
    const cardToCopy = await db.card.findUnique({
      where: {
        id,
        list: {
          board: {
            orgId,
          },
        },
      },
    });

    if (!cardToCopy) {
      return { error: 'Card not found' };
    }

    const lastCard = await db.card.findFirst({
      where: {
        listId: cardToCopy.listId,
      },
      orderBy: {
        order: 'desc',
      },
      select: {
        order: true,
      },
    });

    const newOrder = lastCard ? lastCard.order + 1 : 1;

    card = await db.card.create({
      data: {
        title: `${cardToCopy.title} - Copy`,
        description: cardToCopy.description,
        listId: cardToCopy.listId,
        order: newOrder,
      },
    });
  } catch {
    return {
      error: 'Failed to copy.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: card };
};

export const copyCard = createSafeAction(CopyCard, handler);
```