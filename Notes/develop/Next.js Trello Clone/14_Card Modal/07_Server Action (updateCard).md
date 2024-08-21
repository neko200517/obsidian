## ファイル構成

```ts
.
├── actions
│   └── update-card
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## Schema を作成する

- id, boardId : 必須
- title, description : 任意

### actions/update-card/schema.ts

```ts
import { z } from 'zod';

export const UpdateCard = z.object({
  title: z.optional(
    z
      .string({
        required_error: 'Title is required',
        invalid_type_error: 'Title is required',
      })
      .min(3, {
        message: 'Title is too short',
      })
  ),
  description: z.optional(
    z
      .string({
        required_error: 'Description is required',
        invalid_type_error: 'Description is required',
      })
      .min(3, {
        message: 'Description is too short',
      })
  ),
  id: z.string(),
  boardId: z.string(),
});
```

## Types を作成する

### actions/update-card/types.ts

```ts
import { z } from 'zod';
import { Card } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { UpdateCard } from './schema';

export type InputType = z.infer<typeof UpdateCard>;
export type ReturnType = ActionState<InputType, Card>;
```

## Index を作成する

### actions/update-card/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { InputType, ReturnType } from './types';
import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';
import { UpdateCard } from './schema';

const handler = async (data: InputType): Promise<ReturnType> => {
  console.log('test');
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { id, boardId, ...values } = data;
  let card;

  try {
    card = await db.card.update({
      where: {
        id,
        list: {
          board: {
            orgId,
          },
        },
      },
      data: {
        ...values,
      },
    });
  } catch (error) {
    console.log(error);
    return {
      error: 'Failed to update.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: card };
};

export const updateCard = createSafeAction(UpdateCard, handler);
```