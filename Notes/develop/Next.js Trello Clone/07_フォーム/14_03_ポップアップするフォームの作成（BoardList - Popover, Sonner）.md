## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   └── organization
│   │   │       └── [organizationId]
│   │   │           ├── _components
│   │   │           │   └── board-list.tsx
│   │   │           └── page.tsx
│   │   └── layout.tsx
//...
├── components
│   ├── form
│   │   └── form-popover.tsx
│   └── ui
│       ├── popover.tsx
```

## ShadUI - Popover のインストール

- モーダルを追加

```bash
npx shadcn-ui@latest add popover
```

## ShadUI - Sonner をインストール

- トーストを追加

```bash
npx shadcn-ui@latest add sonner
```

## インストールした popover を修正

- PopoverClose を export に加える

### components/ui/popover.tsx

```tsx
//...

const PopoverClose = PopoverPrimitive.Close;

//...
export { Popover, PopoverTrigger, PopoverContent, PopoverClose };

```

## FormPopover を作成する

- ポップアップするフォームの作成
- Create ボタンを押下するとトーストが表示される

### components/form-popover.tsx

```tsx
'use client';

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
  const { execute, fieldErrors } = useAction(createBoard, {
    onSuceess: (data) => {
      toast.success('Borad created!');
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const onSubmit = (formData: FormData) => {
    const title = formData.get('title') as string;

    execute({ title });
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
          <PopoverClose asChild>
            <Button
              className='h-auto w-auto p2 absolute top-2 right-2 text-neutral-600'
              variant='ghost'
            >
              <X className='h-4 w-4' />
            </Button>
          </PopoverClose>
          <form className='space-y-4' action={onSubmit}>
            <div className='space-y-4'>
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

## PlatformLayout を修正する

- Toaster コンポーネントを追加する

### app/(platform)/layout.tsx

```tsx
import { Toaster } from '@/components/ui/sonner';
import { ClerkProvider } from '@clerk/nextjs';

export default function PlatformLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      <ClerkProvider afterSignOutUrl='/'>
        <Toaster />
        {children}
      </ClerkProvider>
      
    </>
  );
}
```

## BoadList に FormPopover を組み込む

- button になっているところをトリガーにするため FormPopover コンポーネントでラップする
	- button を押下すると FormPopover が表示される

### app/(platform)/(dashboard)/organization/\[organizationId]/\_components/board-list.tsx

```tsx
//...
import FormPopover from '@/components/form/form-popover';

export default function BoardList() {
  return (
      //...
        <FormPopover sideOffset={10} side='right'>
          <div role='button'>
            //...
          </div>
        </FormPopover>
      //...
  );
}
```