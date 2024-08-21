## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       ├── _components
│   │   │   │       │   ├── list-container.tsx
│   │   │   │       │   ├── list-header.tsx
│   │   │   │       │   ├── list-item.tsx
```

## ListHeader を作成する

- data.title が表示されたカードを横並びに表示する
- カードをクリックすると Fomr 要素が表示される
	- Enter, Escape, フォーカスを外した時にデータが update される

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-header.tsx

```tsx
'use client';

import { toast } from 'sonner';
import { useState, useRef, ElementRef } from 'react';
import { useEventListener } from 'usehooks-ts';

import { List } from '@prisma/client';
import { useAction } from '@/hooks/use-action';
import { updateList } from '@/actions/update-list';

import { FormInput } from '@/components/form/form-input';

interface ListHeaderProps {
  data: List;
}

export default function ListHeader({ data }: ListHeaderProps) {
  const { execute, fieldErrors } = useAction(updateList, {
    onSuccess: (data) => {
      toast.success(`Renamed to "${data.title}"`);
      setTitle(data.title);
      disableEditing();
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const [title, setTitle] = useState(data.title);
  const [isEditing, setIsEditing] = useState(false);

  const formRef = useRef<ElementRef<'form'>>(null);
  const inputRef = useRef<ElementRef<'input'>>(null);

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

  const handleSubmit = (formData: FormData) => {
    const id = formData.get('id') as string;
    const boardId = formData.get('boardId') as string;
    const title = formData.get('title') as string;

    if (title === data.title) {
      return disableEditing();
    }

    execute({ id, boardId, title });
  };

  const onBlur = () => {
    formRef.current?.requestSubmit();
  };

  const onKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'Escape') {
      formRef.current?.requestSubmit();
    }
  };

  useEventListener('keydown', onKeyDown);

  return (
    <div className='pt-2 px-2 text-sm font-semibold flex justify-between items-center gap-x-2'>
      {isEditing ? (
        <form ref={formRef} action={handleSubmit} className='flex-1 px-[2px]'>
          <FormInput
            ref={inputRef}
            onBlur={onBlur}
            id='title'
            placeholder='Enter list title.'
            defaultValue={data.title}
            errors={fieldErrors}
            className='text-sm px-[7px] py-1 h-7 font-medium border-transparent hover:border-input focus:border-input transition truncate bg-transparent focus:bg-white'
          />
          <input hidden id='id' name='id' value={data.id} />
          <input hidden id='boardId' name='boardId' value={data.boardId} />
          <button type='submit' hidden></button>
        </form>
      ) : (
        <div
          className='w-full text-sm px-2.5 py-1 h-7 font-medium border-transparent'
          onClick={enableEditing}
        >
          {title}
        </div>
      )}
    </div>
  );
}
```
