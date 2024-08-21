## Actions を作成

- CardModal に Copy, Delete の機能を追加
- 入力しないので form ではなく onClick で実行する
- useCardModal() で CardModal の開閉状態を制御する

```tsx
'use client';

import { toast } from 'sonner';
import { Copy, Trash } from 'lucide-react';
import { useParams } from 'next/navigation';

import { CardWithList } from '@/types';
import { useAction } from '@/hooks/use-action';
import { copyCard } from '@/actions/copy-card';
import { deleteCard } from '@/actions/delete-card';

import { Skeleton } from '@/components/ui/skeleton';
import { Button } from '@/components/ui/button';
import { useCardModal } from '@/hooks/use-card-modal';

interface ActionsProps {
  data: CardWithList;
}

export default function Actions({ data }: ActionsProps) {
  const params = useParams();
  const cardModal = useCardModal();

  const { execute: executeCopyCard, isLoading: isLoadingCopy } = useAction(
    copyCard,
    {
      onSuccess: (data) => {
        toast.success(`Card "${data.title}" copied`);
        cardModal.onClose();
      },
      onError: (error) => {
        toast.error(error);
      },
    }
  );

  const { execute: executeDeleteCard, isLoading: isLoadingDelete } = useAction(
    deleteCard,
    {
      onSuccess: (data) => {
        toast.success(`Card "${data.title}" deleted`);
        cardModal.onClose();
      },
      onError: (error) => {
        toast.error(error);
      },
    }
  );

  const onCopy = () => {
    const boardId = params.boardId as string;
    executeCopyCard({ id: data.id, boardId });
  };

  const onDelete = () => {
    const boardId = params.boardId as string;
    executeDeleteCard({ id: data.id, boardId });
  };

  return (
    <div className='space-y-2 mt-2'>
      <p className='text-xs font-semibold'>Actions</p>
      <Button
        variant='gray'
        className='w-full justify-start'
        size='inline'
        onClick={onCopy}
        disabled={isLoadingCopy}
      >
        <Copy className='h-4 w-4 mr-2' />
        Copy
      </Button>
      <Button
        variant='gray'
        className='w-full justify-start'
        size='inline'
        onClick={onDelete}
        disabled={isLoadingDelete}
      >
        <Trash className='h-4 w-4 mr-2' />
        Delete
      </Button>
    </div>
  );
}

Actions.Skeleton = function ActionsSkeleton() {
  return (
    <div className='space-y-2 mt-2'>
      <Skeleton className='w-20 h-4 bg-neutral-200' />
      <Skeleton className='w-full h-8 bg-neutral-200' />
      <Skeleton className='w-full h-8 bg-neutral-200' />
    </div>
  );
};
```