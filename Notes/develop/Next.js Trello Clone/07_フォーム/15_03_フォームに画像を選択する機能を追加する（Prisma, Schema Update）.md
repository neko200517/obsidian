## ファイル構成

```ts
.
├── actions
│   ├── create-board
│   │   ├── index.ts
│   │   ├── schema.ts
//...
├── components
│   ├── form
│   │   ├── form-popover.tsx
//...
├── prisma
│   └── schema.prisma
//...
```

## Prisma スキーマの修正

- postgreSQL なので小文字のスネークケースに変換している

### prisma/schema.prisma

```tsx
//...
model Board {
  id            String @id @default(uuid())
  orgId         String @map("org_id")
  title         String
  imageId       String @map("image_id")
  imageThumbUrl String @db.Text @map("image_thumb_url")
  imageFullUrl  String @db.Text @map("image_full_url")
  imageUserName String @db.Text @map("image_user_name")
  imageLinkHtml String @db.Text @map("image_link_html")

  createdAt     DateTime @default(now()) @map("created_at")
  updatedAt     DateTime @updatedAt @map("updated_at")

  @@map("boards")
}
```

## データベースを更新する

### データベースのリセット

```bash
npx prisma migrate reset
```

### Prisma Client を生成する

```bash
npx prisma generate
```

### Neonにスキーマを反映する

```bash
npx prisma db push
```

## Create Board Action (Schema) の修正 

- 新たにimage属性を追加
- required を満たしていない場合、エラーメッセージを表示する

### actions/create-board.ts

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
  image: z.string({
    required_error: 'Image is required',
    invalid_type_error: 'Image is required',
  }),
});
```

## Create Board Action (index) の修正 

- 新たにorgId, image情報を追加
	- 受け取ったimage情報を分解してテーブルに追加

### actions/index.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { InputType, ReturnType } from './types';
import { db } from '@/lib/db';
import { createSafeAction } from '@/lib/create-safe-action';
import { CreateBoard } from './schema';

const handler = async (data: InputType): Promise<ReturnType> => {
  const { userId, orgId } = auth();

  if (!userId || !orgId) {
    return {
      error: 'Unauthorized',
    };
  }

  const { title, image } = data;

  const [imageId, imageThumbUrl, imageFullUrl, imageLinkHtml, imageUserName] =
    image.split('|');

  if (
    !imageId ||
    !imageThumbUrl ||
    !imageFullUrl ||
    !imageLinkHtml ||
    !imageUserName
  ) {
    return {
      error: 'Missing fields. Failed to create board.',
    };
  }

  let board;

  try {
    board = await db.board.create({
      data: {
        title,
        orgId,
        imageId,
        imageThumbUrl,
        imageFullUrl,
        imageUserName,
        imageLinkHtml,
      },
    });
  } catch (error) {
    console.log(error);
    return {
      error: 'Failed to create.',
    };
  }

  revalidatePath(`/board/${board.id}`);
  return { data: board };
};

export const createBoard = createSafeAction(CreateBoard, handler);
```

## FormPopover を修正 

- 機能の追加
	- フォームから受け取ったimageの値をActionに渡す 
	- 正常にCreateしたらフォームを閉じる
	- 正常にCreateしたらboardページに遷移する

### components/form/form-popover.tsx

```tsx
'use client';

import { ElementRef, useRef } from 'react';
import { useRouter } from 'next/navigation';
import { toast } from 'sonner';
import { X } from 'lucide-react';

import {
  Popover,
  PopoverClose,
  PopoverContent,
  PopoverTrigger,
} from '@/components/ui/popover';
import { Button } from '@/components//ui/button';

import { useAction } from '@/hooks/use-action';
import { createBoard } from '@/actions/create-board';

import { FormInput } from './form-input';
import FormSubmit from './form-submit';
import FormPicker from './form-picker';

interface FormPopoverProps {
  children: React.ReactNode;
  side?: 'left' | 'right' | 'top' | 'bottom';
  align?: 'start' | 'center' | 'end';
  sideOffset?: number;
}

export default function FormPopover({
  children,
  side = 'bottom',
  align,
  sideOffset = 0,
}: FormPopoverProps) {
  const router = useRouter();
  const closeRef = useRef<ElementRef<'button'>>(null);

  const { execute, fieldErrors } = useAction(createBoard, {
    onSuceess: (data) => {
      toast.success('Borad created!');
      closeRef.current?.click();
      router.push(`/board/${data.id}`);
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const onSubmit = (formData: FormData) => {
    const title = formData.get('title') as string;
    const image = formData.get('image') as string;

    execute({ title, image });
  };

  return (
    <>
      <Popover>
        <PopoverTrigger asChild>{children}</PopoverTrigger>
        <PopoverContent
          className='w-80 pt-3'
          align={align}
          side={side}
          sideOffset={sideOffset}
        >
          <div className='text-sm font-medium text-center text-neutral-600 pb-4'>
            Create board
          </div>
          <PopoverClose ref={closeRef} asChild>
            <Button
              className='h-auto w-auto p2 absolute top-2 right-2 text-neutral-600'
              variant='ghost'
            >
              <X className='h-4 w-4' />
            </Button>
          </PopoverClose>
          <form className='space-y-4' action={onSubmit}>
            <div className='space-y-4'>
              <FormPicker id='image' errors={fieldErrors} />
              <FormInput
                id='title'
                label='Board title'
                type='text'
                errors={fieldErrors}
              />
            </div>
            <FormSubmit className='w-full'>Create</FormSubmit>
          </form>
        </PopoverContent>
      </Popover>
    </>
  );
}
```