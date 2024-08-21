## ファイル構成

```ts
.
├── actions
│   ├── delete-board
│   │   ├── index.ts
│   │   ├── schema.ts
│   │   └── types.ts
```

## DeleteAction - Schema を作成する

- id の検証（必須）

### actions/delete-board/schema.ts

```ts
import { z } from 'zod';

export const DeleteBoard = z.object({
  id: z.string(),
});
```

## DeleteAction - Types を作成する

### actions/delete-board/types.ts

```ts
import { z } from 'zod';
import { Board } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { DeleteBoard } from './schema';

export type InputType = z.infer<typeof DeleteBoard>;
export type RetrunType = ActionState<InputType, Board>;
```

## DeleteAction - Index を作成する

- データの削除を行う
- revalidatePath でURLの更新を行う
- redirect でページの遷移を行う

### actions/delete-board/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { DeleteBoard } from './schema';
import { InputType, RetrunType } from './types';

const handler = async (data: InputType): Promise<RetrunType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { id } = data;
  let board;

  try {
    board = await db.board.delete({
      where: {
        id,
        orgId,
      },
    });
  } catch {
    return {
      error: 'Failed to delete.',
    };
  }

  revalidatePath(`/organization/${orgId}`);
  redirect(`/organization/${orgId}`);
};

export const deleteBoard = createSafeAction(DeleteBoard, handler);
```