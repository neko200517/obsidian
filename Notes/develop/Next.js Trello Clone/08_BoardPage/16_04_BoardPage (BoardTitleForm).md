## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       ├── _components
│   │   │   │       │   └── board-title-form.tsx
```

## BoardTitleForm を作成する

- Board データのタイトルを表示する
- Button を押下するとテキストフォームが表示される
	- setTimeoutでフォーム操作を確実に実行する
- Input 要素に defaultValue を渡して初期値を表示する
- Input 要素を submit および onBlur（フォーカスを外す）と内容が Update される
	- Form 要素に requestSubmit() を実行することで submit を送信する

### app/(platform)/(dashboard)/board/\[boardId]/\_components/board-title-form.tsx

```tsx
'use client';

import { ElementRef, useRef, useState } from 'react';
import { Board } from '@prisma/client';
import { toast } from 'sonner';

import { Button } from '@/components/ui/button';
import { FormInput } from '@/components/form/form-input';
import { updateBoard } from '@/actions/update-board';
import { useAction } from '@/hooks/use-action';

interface BoardTitleFormProps {
  data: Board;
}

export default function BoardTitleForm({ data }: BoardTitleFormProps) {
  const { execute } = useAction(updateBoard, {
    onSuceess: (data) => {
      toast.success(`Board "${data.title}" updated!`);
      setTitle(data.title);
      disableEditing();
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const formRef = useRef<ElementRef<'form'>>(null);
  const inputRef = useRef<ElementRef<'input'>>(null);

  const [title, setTitle] = useState(data.title);
  const [isEditing, setIsEditing] = useState(false);

  const enableEditing = () => {
    setIsEditing(true);
    setTimeout(() => {
      inputRef.current?.focus();
      inputRef.current?.select();
    });
  };

  const disableEditing = () => {
    setIsEditing(false);
  };

  const onSubmit = (formData: FormData) => {
    const title = formData.get('title') as string;

    execute({
      id: data.id,
      title,
    });
  };

  const onBlur = () => {
    // フォーカスが外れたらFormを送信する
    formRef.current?.requestSubmit();
  };

  if (isEditing) {
    return (
      <form
        action={onSubmit}
        ref={formRef}
        className='flex items-center gap-x-2'
      >
        <FormInput
          ref={inputRef}
          id='title'
          onBlur={onBlur}
          defaultValue={title}
          className='text-lg font-bold px-[7px] py-1 h-7 bg-transparent focus-visible:outline-none focus-visible:ring-transparent border-none'
        />
      </form>
    );
  }

  return (
    <>
      <form action='' className='flex gap-x-2'>
        <Button
          onClick={enableEditing}
          variant='transparent'
          className='font-bold text-lg h-auto w-auto p-1 px-2'
        >
          {title}
        </Button>
      </form>
    </>
  );
}
```