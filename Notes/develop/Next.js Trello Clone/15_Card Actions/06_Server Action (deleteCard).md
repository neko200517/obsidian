## ファイル構成

```ts
.
├── actions
│   └── delete-card
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## Schema を作成する

### actions/delete-card/schema.ts

```ts
import { z } from 'zod';

export const DeleteCard = z.object({
  id: z.string(),
  boardId: z.string(),
});
```

## Types を作成する

### actions/delete-card/types.ts

```ts
import { z } from 'zod';
import { Card } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { DeleteCard } from './schema';

export type InputType = z.infer<typeof DeleteCard>;
export type RetrunType = ActionState<InputType, Card>;
```

## Index を作成する

### actions/delete-card/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { DeleteCard } from './schema';
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
    card = await db.card.delete({
      where: {
        id,
        list: {
          board: {
            orgId,
          },
        },
      },
    });
  } catch {
    return {
      error: 'Failed to delete.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: card };
};

export const deleteCard = createSafeAction(DeleteCard, handler);
```