## useQuery.invalidateQueries でキャッシュを最新化 (Header)

### components/modals/card-modal/header.tsx

```tsx
'use client';

import { toast } from 'sonner';
import { ElementRef, useRef, useState } from 'react';
import { Layout } from 'lucide-react';
import { useParams } from 'next/navigation';
import { useQueryClient } from '@tanstack/react-query';

import { CardWithList } from '@/types';
import { Skeleton } from '@/components/ui/skeleton';
import { FormInput } from '@/components/form/form-input';
import { useAction } from '@/hooks/use-action';
import { updateCard } from '@/actions/update-card';

interface HeaderProps {
  data: CardWithList;
}

export default function Header({ data }: HeaderProps) {
  const queryClient = useQueryClient();
  const params = useParams();

  const { execute, fieldErrors } = useAction(updateCard, {
    onSuccess: (data) => {
      queryClient.invalidateQueries({
        queryKey: ['card', data.id],
      });

      // 追加
      queryClient.invalidateQueries({
        queryKey: ['card-logs', data.id],
      });

      toast.success(`Renamed to "${data.title}"`);

      setTitle(data.title);
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const inputRef = useRef<ElementRef<'input'>>(null);

  const [title, setTitle] = useState(data.title);

  const onBlur = () => {
    inputRef.current?.form?.requestSubmit();
  };

  const onSubmit = (formData: FormData) => {
    const title = formData.get('title') as string;
    const id = data.id;
    const boardId = params.boardId as string;

    if (title === data.title) {
      return;
    }

    execute({ title, id, boardId });
  };

  return (
    <div className='flex items-start gap-x-3 mb-6 w-full'>
      <Layout className='h-5 w-5 mt-1 text-neutral-700' />
      <div className='w-full'>
        <form action={onSubmit}>
          <FormInput
            id='title'
            ref={inputRef}
            onBlur={onBlur}
            defaultValue={title}
            errors={fieldErrors}
            className='font-semibold text-xl px-1 text-neutral-700 bg-transparent border-transparent relative -left-1.5 w-[95%] focus-visible:bg-white focus-visible:border-input mb-0.5 truncate'
          />
        </form>
        <p className='text-sm text-muted-foreground'>
          in list <span className='underline'>{data.list.title}</span>
        </p>
      </div>
    </div>
  );
}

Header.Skelton = function HeaderSkeleton() {
  return (
    <div className='flex items-start gap-x-3 mb-6'>
      <Skeleton className='h-6 w-6 mt-1 bg-neutral-200' />
      <div>
        <Skeleton className='w-24 h-6 mb-1 bg-neutral-200' />
        <Skeleton className='w-12 h-4 bg-neutral-200' />
      </div>
    </div>
  );
};
```

## useQuery.invalidateQueries でキャッシュを最新化 (Description)

### components/modals/card-modal/description.tsx

```tsx
'use client';

import { toast } from 'sonner';
import { AlignLeft } from 'lucide-react';
import { useParams } from 'next/navigation';
import { useState, useRef, ElementRef } from 'react';
import { useQueryClient } from '@tanstack/react-query';
import { useEventListener, useOnClickOutside } from 'usehooks-ts';

import { CardWithList } from '@/types';
import { useAction } from '@/hooks/use-action';
import { updateCard } from '@/actions/update-card';

import { Skeleton } from '@/components/ui/skeleton';
import { Button } from '@/components/ui/button';
import FormTextarea from '@/components/form/form-textarea';
import FormSubmit from '@/components/form/form-submit';

interface DescriptionProps {
  data: CardWithList;
}

export default function Description({ data }: DescriptionProps) {
  const params = useParams();
  const queryClient = useQueryClient();

  const [isEditing, setIsEditing] = useState(false);

  const formRef = useRef<ElementRef<'form'>>(null);
  const textareaRef = useRef<ElementRef<'textarea'>>(null);

  const enableEditing = () => {
    setIsEditing(true);
    setTimeout(() => {
      textareaRef.current?.focus();
    });
  };

  const disableEditing = () => {
    setIsEditing(false);
  };

  const onKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'Escape') {
      disableEditing();
    }
  };

  useEventListener('keydown', onKeyDown);
  useOnClickOutside(formRef, disableEditing);

  const { execute, fieldErrors } = useAction(updateCard, {
    onSuccess: (data) => {
      queryClient.invalidateQueries({
        queryKey: ['card', data.id],
      });

      // 追加
      queryClient.invalidateQueries({
        queryKey: ['card-logs', data.id],
      });

      toast.success(`Card "${data.title}" updated`);
      disableEditing();
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const onSubmit = (formData: FormData) => {
    const boardId = params.boardId as string;
    const description = formData.get('description') as string;

    execute({
      id: data.id,
      boardId,
      description,
    });
  };

  return (
    <div className='flex items-start gap-x-3 w-full'>
      <AlignLeft className='h-5 w-5 mt-0.5 text-neutral-700' />
      <div className='w-full'>
        <p className='font-semibold text-neutral-700 mb-2'>Description</p>

        {isEditing ? (
          <form ref={formRef} className='space-y-2' action={onSubmit}>
            <FormTextarea
              ref={textareaRef}
              id='description'
              className='w-full mt-2'
              placeholder='Add a more detailed description'
              defaultValue={data.description || undefined}
              errors={fieldErrors}
            />
            <div className='flex items-center gap-x-2'>
              <FormSubmit>Save</FormSubmit>
              <Button
                type='button'
                size='sm'
                variant='ghost'
                onClick={disableEditing}
              >
                Cancel
              </Button>
            </div>
          </form>
        ) : (
          <div
            role='button'
            onClick={enableEditing}
            className='min-h-[78px] bg-neutral-200 text-sm font-medium py-3 px-3.5 rounded-md'
          >
            {data.description || 'Add a more detailed description...'}
          </div>
        )}
      </div>
    </div>
  );
}

Description.Skelelton = function DescriptionSkeleton() {
  return (
    <div className='flex items-start gap-x-3 w-full'>
      <Skeleton className='h-6 w-6 bg-neutral-200' />
      <div className='w-full'>
        <Skeleton className='w-24 h-6 mb-2 bg-neutral-200' />
        <Skeleton className='w-full h-[78px] bg-neutral-200' />
      </div>
    </div>
  );
};
```