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
│   │   │   │       │   ├── list-form.tsx
│   │   │   │       │   └── list-wrapper.tsx
```

## ListContainer を作成する

- List と Card が 1対n の型情報を引数にデータを受け取る

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-container.tsx

```tsx
'use client';

import { ListWithCards } from '@/types';

import ListForm from './list-form';

interface ListContainerProps {
  boardId: string;
  data: ListWithCards[];
}

export default function ListContainer({ boardId, data }: ListContainerProps) {
  return (
    <ol>
      <ListForm />
      <div className='flex-shrink-0 w-1' />
    </ol>
  );
}
```

## ListForm を作成する

- button要素を押下するとinput要素が表示される
- useEventListener で 要素にイベントリスナーを追加 
	- Escapeキーを押下すると Form 要素が非表示になる
- useOnClickOutside で要素外をクリックした時に handler を実行する
	- Form の要素外をクリックすると Form 要素を非表示
- 隠し要素の input 要素に boardId を仕込んでおいて Form が Submit したときに情報を送信する
- 登録が成功したら router.refresh() でページを更新

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-form.tsx

```tsx
'use client';

import { toast } from 'sonner';
import { Plus, X } from 'lucide-react';
import { useParams, useRouter } from 'next/navigation';
import { useState, useRef, ElementRef } from 'react';
import { useEventListener, useOnClickOutside } from 'usehooks-ts';

import { useAction } from '@/hooks/use-action';
import { createList } from '@/actions/create-list';

import { Button } from '@/components/ui/button';
import { FormInput } from '@/components/form/form-input';
import FormSubmit from '@/components/form/form-submit';

import ListWrapper from './list-wrapper';

export default function ListForm() {
  const router = useRouter();
  const params = useParams();

  const formRef = useRef<ElementRef<'form'>>(null);
  const inputRef = useRef<ElementRef<'input'>>(null);

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

  const { execute, fieldErrors } = useAction(createList, {
    onSuceess: (data) => {
      toast.success(`List "${data.title}" created`);
      disableEditing();
      router.refresh();
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const onKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'Escape') {
      disableEditing();
    }
  };

  // window 全体に keydown イベントリスナーを追加
  useEventListener('keydown', onKeyDown);

  // Form 要素外をクリックしたときに disableEditing を実行する
  useOnClickOutside(formRef, disableEditing);

  const onSubmit = (formData: FormData) => {
    const title = formData.get('title') as string;
    const boardId = formData.get('boardId') as string;

    execute({ title, boardId });
  };

  if (isEditing) {
    return (
      <ListWrapper>
        <form
          ref={formRef}
          className='w-full p-3 rounded-md bg-white space-y-4 shadow-md'
          action={onSubmit}
        >
          <FormInput
            ref={inputRef}
            id='title'
            className='text-sm px-2 py-1 h-7 font-medium border-transparent hover:border-input focus:border-input transition'
            placeholder='Enter list item...'
            errors={fieldErrors}
          />
          <input hidden value={params.boardId} name='boardId' />
          <div className='flex items-center gap-x-1'>
            <FormSubmit>Add List</FormSubmit>
            <Button onClick={disableEditing} size='sm' variant='ghost'>
              <X className='h-5 w-5' />
            </Button>
          </div>
        </form>
      </ListWrapper>
    );
  }

  return (
    <ListWrapper>
      <button
        onClick={enableEditing}
        className='w-full rounded-md bg-white/80 hover:bg-white/50 transition p-3 flex items-center font-medium text-sm'
      >
        <Plus className='h-4 w-4 mr-2' />
        Add a list
      </button>
    </ListWrapper>
  );
}
```

## ListWrapper を作成する

- 仮

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-wrapper.tsx

```tsx
import React from 'react';

interface ListWrapperProps {
  children: React.ReactNode;
}

export default function ListWrapper({ children }: ListWrapperProps) {
  return <li className='shrink-0 h-full w-[272px] select-none'>{children}</li>;
}
```