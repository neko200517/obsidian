## ファイル構成

```ts
.
├── actions
│   └── create-board.ts
```

## ライブラリをインストール

```bash
npm i zod
```

## Board の登録アクション

- zod による入力値のバリデーション
- revalidatePath によるテーブルの再読込

### actions/create-board.ts

```ts
'use server';

import { z } from 'zod';
import { auth } from '@clerk/nextjs/server';
import { redirect } from 'next/navigation';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';

export type State = {
  errors?: {
    title?: string[];
  };
  message?: string | null;
};

const CreateBoard = z.object({
  title: z.string().min(3, {
    message: 'Minumum length of 3 letters is required ',
  }),
});

export async function create(
  prevState: State,
  formData: FormData
): Promise<State> {
  const validatedFields = CreateBoard.safeParse({
    title: formData.get('title'),
  });

  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing fields.',
    };
  }

  const { title } = validatedFields.data;

  try {
    await db.board.create({
      data: {
        title,
      },
    });
  } catch (error) {
    return {
      message: 'Database Error',
    };
  }

  revalidatePath(`/organization/${auth().orgId}`);
  redirect(`/organization/${auth().orgId}`);
}
```