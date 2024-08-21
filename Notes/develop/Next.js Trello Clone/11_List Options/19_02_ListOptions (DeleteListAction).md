## ファイル構成

```ts
.
├── actions
│   └── delete-list
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## DeleteAction - Schema を作成する

- title（必須、最低文字数）と boardId（必須）の検証
- InputType の決定

### actions/delete-list/schema.ts

```ts
import { z } from 'zod';

export const DeleteList = z.object({
  id: z.string(),
  boardId: z.string(),
});
```

## DeleteAction - Types を作成する

- InputType と ReturnType の型を決める

### actions/delete-list/types.ts

```ts
import { z } from 'zod';
import { List } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { DeleteList } from './schema';

export type InputType = z.infer<typeof DeleteList>;
export type RetrunType = ActionState<InputType, List>;
```

## DeleteAction - Index を作成する

### actions/delete-list/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { DeleteList } from './schema';
import { InputType, RetrunType } from './types';

const handler = async (data: InputType): Promise<RetrunType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { id, boardId } = data;
  let list;

  try {
    list = await db.list.delete({
      where: {
        id,
        boardId,
        board: {
          orgId,
        },
      },
    });
  } catch {
    return {
      error: 'Failed to delete.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: list };
};

export const deleteList = createSafeAction(DeleteList, handler);
```