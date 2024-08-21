## ファイル構成

```ts
.
├── actions
│   └── update-card-order
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## Schema を作成する

### actions/update-card-order/schema.ts

```ts
import { z } from 'zod';

export const UpdateCardOrder = z.object({
  items: z.array(
    z.object({
      id: z.string(),
      title: z.string(),
      order: z.number(),
      listId: z.string(),
      createdAt: z.date(),
      updatedAt: z.date(),
    })
  ),
  boardId: z.string(),
});
```

## Types を作成する

### actions/update-card-order/types.ts

```ts
import { z } from 'zod';
import { Card } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { UpdateCardOrder } from './schema';

export type InputType = z.infer<typeof UpdateCardOrder>;
export type ReturnType = ActionState<InputType, Card[]>;
```

## Index を作成する

### actions/update-card-order/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { InputType, ReturnType } from './types';
import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';
import { UpdateCardOrder } from './schema';

const handler = async (data: InputType): Promise<ReturnType> => {
  console.log('test');
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { boardId, items } = data;
  let updateCards;

  try {
    const transction = items.map((card) =>
      db.card.update({
        where: {
          id: card.id,
          list: {
            board: {
              orgId,
            },
          },
        },
        data: {
          order: card.order,
          listId: card.listId,
        },
      })
    );

    updateCards = await db.$transaction(transction);
  } catch (error) {
    console.log(error);
    return {
      error: 'Failed to reorder.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: updateCards };
};

export const updateCardOrder = createSafeAction(UpdateCardOrder, handler);
```