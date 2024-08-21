## ファイル構成

```ts
.
├── actions
│   └── update-list
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## Shema を作成する

- title, id, boardId を必須にする

### actions/update-list/schema.ts

```tsx
import { z } from 'zod';

export const UpdateList = z.object({
  title: z
    .string({
      required_error: 'Title is required',
      invalid_type_error: 'Title is required',
    })
    .min(3, {
      message: 'Title is too short.',
    }),
  id: z.string(),
  boardId: z.string(),
});
```

## Types を作成する

### actions/update-list/types.ts

```ts
import { z } from 'zod';
import { List } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { UpdateList } from './schema';

export type InputType = z.infer<typeof UpdateList>;
export type ReturnType = ActionState<InputType, List>;
```

## Index を作成する

- list テーブルを update する

### actions/update-list/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { InputType, ReturnType } from './types';
import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';
import { UpdateList } from './schema';

const handler = async (data: InputType): Promise<ReturnType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { id, boardId, title } = data;

  let list;

  try {
    list = await db.list.update({
      where: {
        id,
        boardId,
        board: {
          orgId,
        },
      },
      data: {
        title,
      },
    });
  } catch (error) {
    console.log(error);
    return {
      error: 'Failed to update.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: list };
};

export const updateList = createSafeAction(UpdateList, handler);
```