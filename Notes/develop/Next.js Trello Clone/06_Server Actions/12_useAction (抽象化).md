## 概要

フォームデータの受け渡し、結果、バリデーション、例外メッセージを汎用的にするための仕組みを作成する

## ファイル構成

```ts
.
├── actions
│   ├── create-board
│   │   ├── index.ts
│   │   ├── schema.ts
│   │   └── types.ts
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   └── organization
│   │   │       └── [organizationId]
│   │   │           └── form.tsx
//...
├── hooks
│   └── use-action.ts
├── lib
│   └── create-safe-action.ts
//...
```

## CreateSafeAction を作成する

- fieldErrors 
	- zod のバリデーションエラーが返る 
- error 
	- その他のエラーが返る 
- data 
	- 正常に処理された場合の値が返る

### lib/create-safe-action.ts

```ts
import { z } from 'zod';

export type FieldErros<T> = {
  [K in keyof T]?: string[];
};

export type ActionState<TInput, TOutput> = {
  fieldErros?: FieldErros<TInput>;
  error?: string | null;
  data?: TOutput;
};

export const createSafeAction = <TInput, TOutput>(
  schema: z.Schema<TInput>,
  handler: (validateData: TInput) => Promise<ActionState<TInput, TOutput>>
) => {
  return async (data: TInput): Promise<ActionState<TInput, TOutput>> => {
    const validationResult = schema.safeParse(data);
    if (!validationResult.success) {
      return {
        fieldErros: validationResult.error.flatten()
          .fieldErrors as FieldErros<TInput>,
      };
    }

    return handler(validationResult.data);
  };
};
```

## CreateBoardAction - Index を作成する

DBの登録などを担当

### actions/create-board/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { InputType, ReturnType } from './types';
import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';
import { CreateBoard } from './schema';

const handler = async (data: InputType): Promise<ReturnType> => {
  const { userId } = auth();

  if (!userId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { title } = data;

  let board;

  try {
    board = await db.board.create({
      data: {
        title,
      },
    });
  } catch (error) {
    return {
      error: 'Failed to create.',
    };
  }

  revalidatePath(`/board/${board.id}`);
  return { data: board };
};

export const createBoard = createSafeAction(CreateBoard, handler);
```

## CreateBoardAction - Schema を作成する

バリデーションを担当

### actions/create-board/schema.ts

```ts
import { z } from 'zod';

export const CreateBoard = z.object({
  title: z
    .string({
      required_error: 'Title is required',
      invalid_type_error: 'Title is required',
    })
    .min(3, {
      message: 'Title is too short.',
    }),
});
```

## CreateBoardAction - Types を作成する

型を担当

### actions/create-board/types.ts

```ts
import { z } from 'zod';
import { Board } from '@prisma/client';

import { ActionState } from '@/lib/create-safe-action';

import { CreateBoard } from './schema';

export type InputType = z.infer<typeof CreateBoard>;
export type ReturnType = ActionState<InputType, Board>;
```

## UseAction を作成する

フロントエンドから使いやすくするためのカスタムフック

### hooks/use-action.ts

```ts
import { useState, useCallback } from 'react';

import { ActionState, FieldErros } from '@/lib/create-safe-action';

type Action<TInput, TOutput> = (
  data: TInput
) => Promise<ActionState<TInput, TOutput>>;

interface UseActionOptions<TOutput> {
  onSuceess?: (data: TOutput) => void;
  onError?: (error: string) => void;
  onComplete?: () => void;
}

export const useAction = <TInput, TOutput>(
  action: Action<TInput, TOutput>,
  options: UseActionOptions<TOutput> = {}
) => {
  const [fieldErrors, setFieldErrors] = useState<
    FieldErros<TInput> | undefined
  >(undefined);
  const [error, setError] = useState<string | undefined>(undefined);
  const [data, setData] = useState<TOutput | undefined>(undefined);
  const [isLoading, setIsLoading] = useState<boolean>(false);

  const execute = useCallback(
    async (input: TInput) => {
      setIsLoading(true);

      try {
        const result = await action(input);

        if (!result) {
          return;
        }

        setFieldErrors(result.fieldErros);

        if (result.error) {
          setError(result.error);
          options.onError?.(result.error);
        }

        if (result.data) {
          setData(result.data);
          options.onSuceess?.(result.data);
        }
      } finally {
        setIsLoading(false);
        options.onComplete?.();
      }
    },
    [action, options]
  );

  return { execute, fieldErrors, error, data, isLoading };
};
```

## Formを変更する

- useAction を使用して execute, fieldsErrors を取得
	- 第一引数にCreateBoard の処理（index.tsx）を引数にする
	- 第二引数にコールバックを処理する
		- onSucess: 登録成功
		- onError: 登録失敗
- execute に値を渡す
- エラーがある場合 fieldsErrors に文字列型の配列が返る

### app/(platform)/(dashboard )/organization/\[organizationId]/form.tsx

```tsx
'use client';

import { createBoard } from '@/actions/create-board';

import FormInput from './form-input';
import FormButton from './form-button';
import { useAction } from '@/hooks/use-action';

export default function Form() {
  const { execute, fieldErrors } = useAction(createBoard, {
    onSuceess: (data) => {
      console.log(data, 'SUCCESS!');
    },
    onError: (error) => {
      console.log(error);
    },
  });

  const onSubmit = (formData: FormData) => {
    const title = formData.get('title') as string;

    execute({ title });
  };

  return (
    <form action={onSubmit}>
      <div className='flex flex-col space-y-2'>
        <FormInput errors={fieldErrors} />
      </div>
      <FormButton />
    </form>
  );
}
```