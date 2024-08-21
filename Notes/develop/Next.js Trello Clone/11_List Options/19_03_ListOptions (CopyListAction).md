## ファイル構成

```ts
.
├── actions
│   └── copy-list
│       ├── index.ts
│       ├── schema.ts
│       └── types.ts
```

## CopyAction - Schema を作成する

### actions/copy-list/schema.ts

```ts
import { z } from 'zod';

export const CopyList = z.object({
  id: z.string(),
  boardId: z.string(),
});
```

## CopyAction - Types を作成する

### actions/copy-list/types.ts

```ts
import { z } from 'zod';
import { List } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { CopyList } from './schema';

export type InputType = z.infer<typeof CopyList>;
export type RetrunType = ActionState<InputType, List>;
```

## CopyAction - Index を作成する

- コピー元のデータを新しいデータにコピーする
	- プライマリキー、外部キーはコピーできない
	- 結合しているcardsも含めて登録するには createMany を使用する

### actions/copy-list/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';

import { CopyList } from './schema';
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

    const listToCopy = await db.list.findUnique({
      where: {
        id,
        boardId,
        board: {
          orgId,
        },
      },
      include: {
        cards: true,
      },
    });

    if (!listToCopy) {
      return {
        error: 'List not found',
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
        title: `${listToCopy.title} - Copy`,
        boardId: listToCopy.boardId,
        order: newOrder,
        cards: {
          createMany: {
            data: listToCopy.cards.map((card) => ({
              title: card.title,
              order: card.order,
              description: card.description,
            })),
          },
        },
      },
      include: {
        cards: true,
      },
    });
  } catch {
    return {
      error: 'Failed to copy.',
    };
  }

  revalidatePath(`/board/${boardId}`);
  return { data: list };
};

export const copyList = createSafeAction(CopyList, handler);
```