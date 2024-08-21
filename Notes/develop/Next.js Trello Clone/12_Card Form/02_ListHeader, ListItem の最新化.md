## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       ├── _components
│   │   │   │       │   ├── list-header.tsx
│   │   │   │       │   ├── list-item.tsx
```

## ListHeader を最新化

ListOptions の「Add card...」を選択した時の関数を追加

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
import ListOptions from './list-options';

interface ListHeaderProps {
  onAddCard: () => void;
  data: List;
}

export default function ListHeader({ onAddCard, data }: ListHeaderProps) {
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
      <ListOptions onAddCard={onAddCard} data={data} />
    </div>
  );
}
```

## ListItem を最新化

- CardItem と CardForm を組み込む

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-item.tsx

```tsx
'use client';

import { ElementRef, useRef, useState } from 'react';

import { cn } from '@/lib/utils';
import { ListWithCards } from '@/types';

import ListHeader from './list-header';
import CardForm from './card-form';
import CardItem from './card-item';

interface ListItemProps {
  index: number;
  data: ListWithCards;
}

export default function ListItem({ index, data }: ListItemProps) {
  const textareaRef = useRef<ElementRef<'textarea'>>(null);

  const [isEditing, setIsEditing] = useState(false);

  const disableEditing = () => {
    setIsEditing(false);
  };

  const enableEditing = () => {
    setIsEditing(true);
    setTimeout(() => {
      textareaRef.current?.focus();
    });
  };

  return (
    <li className='shrink-0 h-full w-[272px] select-none'>
      <div className='w-full rounded-md bg-[#f1f2f4] shadow-md pb-2'>
        <ListHeader onAddCard={enableEditing} data={data} />
        <ol
          className={cn(
            'mx-1 px-1 py-0.5 flex flex-col gap-y-2',
            data.cards.length > 0 ? 'mt-2' : 'mt-0'
          )}
        >
          {data.cards.map((card, index) => (
            <CardItem key={card.id} index={index} data={card} />
          ))}
        </ol>
        <CardForm
          listId={data.id}
          isEditing={isEditing}
          enableEditing={enableEditing}
          disableEditing={disableEditing}
        />
      </div>
    </li>
  );
}
```