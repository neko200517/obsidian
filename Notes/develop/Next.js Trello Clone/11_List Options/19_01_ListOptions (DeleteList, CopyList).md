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
│   │   │   │       │   ├── list-options.tsx
```

## ListContainer に ListItem を組み込む

- ListOptions に onAddCard, data（List型）を渡す
- onAddCard は後述

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-header.tsx

```tsx
//...
import ListOptions from './list-options';

export default function ListHeader({ data }: ListHeaderProps) {
//...
  return (
      //...
      <ListOptions onAddCard={() => {}} data={data} />
      //...
  );
}
```

## ListOptions を作成する

- Add List, Copy List, Delete List のメニューを表示
- Copy List, Delete List の実装を行う

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-options.tsx

```tsx
'use client';

import { ElementRef, useRef } from 'react';
import { toast } from 'sonner';
import { List } from '@prisma/client';
import { MoreHorizontal, X } from 'lucide-react';

import {
  Popover,
  PopoverClose,
  PopoverContent,
  PopoverTrigger,
} from '@/components/ui/popover';
import { Button } from '@/components/ui/button';
import { Separator } from '@/components/ui/separator';

import FormSubmit from '@/components/form/form-submit';
import { useAction } from '@/hooks/use-action';
import { deleteList } from '@/actions/delete-list';
import { copyList } from '@/actions/copy-list';

interface ListOptionsProps {
  onAddCard: () => void;
  data: List;
}

export default function ListOptions({ onAddCard, data }: ListOptionsProps) {
  const closeRef = useRef<ElementRef<'button'>>(null);

  const { execute: executeCopy } = useAction(copyList, {
    onSuccess: (data) => {
      toast.success(`List "${data.title}" copied`);
      closeRef.current?.click();
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const { execute: executeDelete } = useAction(deleteList, {
    onSuccess: (data) => {
      toast.success(`List "${data.title}" deleted`);
      closeRef.current?.click();
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const onCopy = (formData: FormData) => {
    const id = formData.get('id') as string;
    const boardId = formData.get('boardId') as string;

    executeCopy({ id, boardId });
  };

  const onDelete = (formData: FormData) => {
    const id = formData.get('id') as string;
    const boardId = formData.get('boardId') as string;

    executeDelete({ id, boardId });
  };

  return (
    <Popover>
      <PopoverTrigger asChild>
        <Button className='h-auto w-auto p-2' variant='ghost'>
          <MoreHorizontal className='h-4 w-4' />
        </Button>
      </PopoverTrigger>
      <PopoverContent className='px-0 pt-3 pb-3' side='bottom' align='start'>
        <div className='text-sm font-medium text-center text-neutral-600 pb-4'>
          Delete List
        </div>

        <PopoverClose ref={closeRef} asChild>
          <Button
            className='h-auto w-auto p-2 absolute top-2 right-2 text-neutral-600'
            variant='ghost'
          >
            <X className='h-4 w-4' />
          </Button>
        </PopoverClose>

        <Button
          onClick={onAddCard}
          className='rounded-none w-full h-auto p-2 px-5 justify-start font-normal text-sm'
          variant='ghost'
        >
          Add card...
        </Button>

        <form action={onCopy}>
          <input hidden id='id' name='id' value={data.id} />
          <input hidden id='boardId' name='boardId' value={data.boardId} />
          <FormSubmit
            className='rounded-none w-full p-2 px-5 justify-start font-normal text-sm'
            variant='ghost'
          >
            Copy list...
          </FormSubmit>
        </form>

        <Separator />

        <form action={onDelete}>
          <input hidden id='id' name='id' value={data.id} />
          <input hidden id='boardId' name='boardId' value={data.boardId} />
          <FormSubmit
            className='rounded-none w-full p-2 px-5 justify-start font-normal text-sm'
            variant='ghost'
          >
            Delete this list...
          </FormSubmit>
        </form>
      </PopoverContent>
    </Popover>
  );
}
```