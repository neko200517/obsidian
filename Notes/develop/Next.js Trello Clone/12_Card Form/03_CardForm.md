## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       ├── _components
│   │   │   │       │   ├── card-form.tsx
│   │   │   │       │   ├── card-item.tsx
```

## CardForm を作成する

- forardRef で Textarea としてコンポーネントを登録
- URL パスから boardId を取得
- フォームを実行すると listId、boardId、title を送信
- 新規 Card を登録
- Textarea に対してキー入力の登録 
	- Shift を押さずに Enter をすると登録
- displayName で html の表示名を登録

### app/(platform)/(dashboard)/board/\[boardId]/\_components/card-form.tsx

```tsx
'use client';

import { forwardRef, useRef, ElementRef, KeyboardEventHandler } from 'react';
import { useParams } from 'next/navigation';
import { toast } from 'sonner';
import { useOnClickOutside, useEventListener } from 'usehooks-ts';

import { Plus, X } from 'lucide-react';

import { Button } from '@/components/ui/button';
import FormTextarea from '@/components/form/form-textarea';
import FormSubmit from '@/components/form/form-submit';
import { useAction } from '@/hooks/use-action';
import { createCard } from '@/actions/create-card';

interface CardFromProps {
  listId: string;
  isEditing: boolean;
  enableEditing: () => void;
  disableEditing: () => void;
}

const CardForm = forwardRef<HTMLTextAreaElement, CardFromProps>(
  function CardForm({ listId, isEditing, enableEditing, disableEditing }, ref) {
    const params = useParams();
    const formRef = useRef<ElementRef<'form'>>(null);

    const { execute, fieldErrors } = useAction(createCard, {
      onSuccess: (data) => {
        toast.success(`Card "${data.title} created`);
        disableEditing();
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

    useOnClickOutside(formRef, disableEditing);
    useEventListener('keydown', onKeyDown);

    const onTextareaKeyDown: KeyboardEventHandler<HTMLTextAreaElement> = (
      e
    ) => {
      if (e.key === 'Enter' && !e.shiftKey) {
        e.preventDefault();
        formRef.current?.requestSubmit();
      }
    };

    const onSubmit = (formData: FormData) => {
      const title = formData.get('title') as string;
      const listId = formData.get('listId') as string;
      const boardId = params.boardId as string;

      execute({ title, listId, boardId });
    };

    if (isEditing) {
      return (
        <form
          ref={formRef}
          action={onSubmit}
          className='m-1 py-0.5 px-1 space-y-4'
        >
          <FormTextarea
            ref={ref}
            id='title'
            placeholder='Enter a title for this card...'
            onKeyDown={onTextareaKeyDown}
            errors={fieldErrors}
          />
          <input hidden id='listId' name='listId' value={listId} />
          <div className='flex items-center gap-x-1'>
            <FormSubmit>Add card</FormSubmit>
            <Button size='sm' variant='ghost' onClick={disableEditing}>
              <X className='h-5 w-5' />
            </Button>
          </div>
        </form>
      );
    }

    return (
      <div className='pt-2 px-2'>
        <Button
          onClick={enableEditing}
          className='h-auto px-2 py1.5 w-full justify-start text-muted-foreground text-sm'
          size='sm'
          variant='ghost'
        >
          <Plus className='h-4 w-4 mr-2' />
          Add a card
        </Button>
      </div>
    );
  }
);

CardForm.displayName = 'CardForm';

export default CardForm;
```
