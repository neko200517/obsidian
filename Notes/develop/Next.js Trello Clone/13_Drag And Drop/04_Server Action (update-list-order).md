## ファイル構成

```ts
.
├── actions
│   └── update-list-order
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## Schema を作成する

- items 
	- リストデータのorderを変更する

### actions/update-list-order/schema.ts

```ts
import { z } from 'zod';

export const UpdateListOrder = z.object({
  items: z.array(
    z.object({
      id: z.string(),
      title: z.string(),
      order: z.number(),
      createdAt: z.date(),
      updatedAt: z.date(),
    })
  ),
  boardId: z.string(),
});
```

## Types を作成する

### actions/update-list-order/types.ts

```ts
import { z } from 'zod';
import { List } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { UpdateListOrder } from './schema';

export type InputType = z.infer<typeof UpdateListOrder>;
export type ReturnType = ActionState<InputType, List[]>;
```

## Index を作成する

- $transction
	- 複数 update を実行するためトランザクションを開始する
		- commit, rollback は自動で実行される

### actions/update-list-order/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { InputType, ReturnType } from './types';
import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';
import { UpdateListOrder } from './schema';

const handler = async (data: InputType): Promise<ReturnType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { boardId, items } = data;
  let lists;

  try {
    const transction = items.map((list) =>
      db.list.update({
        where: {
          id: list.id,
          board: {
            orgId,
          },
        },
        data: {
          order: list.order,
        },
      })
    );

    lists = await db.$transaction(transction);
  } catch (error) {
    console.log(error);
    return {
      error: 'Failed to reorder.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: lists };
};

export const updateListOrder = createSafeAction(UpdateListOrder, handler);
```