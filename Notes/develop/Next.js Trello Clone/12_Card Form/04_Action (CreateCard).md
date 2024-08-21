## ファイル構成

```ts
.
├── actions
│   └── create-list
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## CreateCardAction - Schema を作成する

- title（必須、最低文字数）と boardId（必須）、listId（必須）の検証

### actions/create-list/schema.ts

```ts
import { z } from 'zod';

export const CreateCard = z.object({
  title: z
    .string({
      required_error: 'Title is required',
      invalid_type_error: 'Title is required',
    })
    .min(3, {
      message: 'Title is too short.',
    }),
  boardId: z.string(),
  listId: z.string(),
});
```

## CreateCardAction - Types を作成する

- InputType と ReturnType の型を決める

### actions/create-list/types.ts

```ts
import { z } from 'zod';
import { Card } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { CreateCard } from './schema';

export type InputType = z.infer<typeof CreateCard>;
export type ReturnType = ActionState<InputType, Card>;
```

## CreateCardAction - Index を作成する

- 登録済のデータからorderを設定

### actions/create-list/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { InputType, ReturnType } from './types';
import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';
import { CreateCard } from './schema';

const handler = async (data: InputType): Promise<ReturnType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { title, listId, boardId } = data;

  let card;

  try {
    const list = await db.list.findUnique({
      where: {
        id: listId,
        board: {
          orgId,
        },
      },
    });

    if (!list) {
      return {
        error: 'List not found',
      };
    }

    const lastCard = await db.card.findFirst({
      where: { listId },
      orderBy: { order: 'desc' },
      select: { order: true },
    });

    const newOrder = lastCard ? lastCard.order + 1 : 1;

    card = await db.card.create({
      data: {
        title,
        listId,
        order: newOrder,
      },
    });
  } catch (error) {
    console.log(error);
    return {
      error: 'Failed to create.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: card };
};

export const createCard = createSafeAction(CreateCard, handler);
```