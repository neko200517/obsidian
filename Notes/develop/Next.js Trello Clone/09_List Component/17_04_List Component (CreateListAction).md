## ファイル構成

```ts
.
├── actions
│   └── create-list
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## UpdateAction - Schema を作成する

- title（必須、最低文字数）と boardId（必須）の検証
- InputType の決定

### actions/update-board/schema.ts

```ts
import { z } from 'zod';

export const CreateList = z.object({
  title: z
    .string({
      required_error: 'Title is required',
      invalid_type_error: 'Title is required',
    })
    .min(3, {
      message: 'Title is to short',
    }),
  boardId: z.string(),
});
```

## UpdateAction - Types を作成する

- InputType と ReturnType の型を決める

### actions/update-board/types.ts

```ts
import { z } from 'zod';
import { List } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { CreateList } from './schema';

export type InputType = z.infer<typeof CreateList>;
export type RetrunType = ActionState<InputType, List>;
```

## UpdateAction - Index を作成する

- データの登録を行う
- board の存在確認を行う
- 最後の list.order + 1 を新しい order に指定する（存在しなければ 1 を設定）
- revalidatePath で指定したURLの内容を更新する

### actions/update-board/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { CreateList } from './schema';
import { InputType, RetrunType } from './types';

const handler = async (data: InputType): Promise<RetrunType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { title, boardId } = data;
  let list;

  try {
    const board = await db.board.findUnique({
      where: {
        id: boardId,
        orgId,
      },
    });

    if (!board) {
      return {
        error: 'Board not found',
      };
    }

    const lastList = await db.list.findFirst({
      where: { boardId },
      orderBy: { order: 'desc' },
      select: { order: true },
    });

    const newOrder = lastList ? lastList.order + 1 : 1;

    list = await db.list.create({
      data: {
        title,
        boardId,
        order: newOrder,
      },
    });
  } catch {
    return {
      error: 'Failed to create.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: list };
};

export const createList = createSafeAction(CreateList, handler);
```