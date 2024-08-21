## ファイル構成

```ts
.
├── actions
│   └── update-board
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## UpdateAction - Schema を作成する

- title（必須、最低文字数）と id（必須）の検証
- InputType の決定

### actions/update-board/schema.ts

```ts
import { z } from 'zod';

export const UpdateBoard = z.object({
  title: z
    .string({
      required_error: 'Title is required',
      invalid_type_error: 'Title is required',
    })
    .min(3, {
      message: 'Title is to short',
    }),
  id: z.string(),
});
```

## UpdateAction - Types を作成する

- InputType と ReturnType の型を決める

### actions/update-board/types.ts

```ts
import { z } from 'zod';
import { Board } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { UpdateBoard } from './schema';

export type InputType = z.infer<typeof UpdateBoard>;
export type RetrunType = ActionState<InputType, Board>;
```

## UpdateAction - Index を作成する

- データの更新を行う
- revalidatePath で指定したURLの内容を更新する

### actions/update-board/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { UpdateBoard } from './schema';
import { InputType, RetrunType } from './types';

const handler = async (data: InputType): Promise<RetrunType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { title, id } = data;
  let board;

  try {
    board = await db.board.update({
      where: {
        id,
        orgId,
      },
      data: {
        title,
      },
    });
  } catch {
    return {
      error: 'Failed to update.',
    };
  }

  revalidatePath(`/board/${id}`);
  return { data: board };
};

export const updateBoard = createSafeAction(UpdateBoard, handler);
```