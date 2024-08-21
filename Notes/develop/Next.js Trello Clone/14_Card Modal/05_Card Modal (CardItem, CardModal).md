## CardItem から CardModal を呼び出せるように修正する

- カードをクリックするとモーダルダイアログが表示される
	- useCardModal() を使って開閉状態を制御する

### app/(platform)/(dashboard)/board/\[boardId]/\_components/card-item.tsx

```tsx
'use client';

import { Card } from '@prisma/client';
import { Draggable } from '@hello-pangea/dnd';
import { useCardModal } from '@/hooks/use-card-modal';

interface CardItemProps {
  index: number;
  data: Card;
}

export default function CardItem({ index, data }: CardItemProps) {
  const cardModal = useCardModal();

  return (
    <Draggable draggableId={data.id} index={index}>
      {(provided) => (
        <div
          {...provided.draggableProps}
          {...provided.dragHandleProps}
          ref={provided.innerRef}
          role='button'
          onClick={() => cardModal.onOpen(data.id)}
          className='truncate border-2 border-transparent hover:border-black py-2 px-3 text-sm bg-white rounded-md shadow-sm'
        >
          {data.title}
        </div>
      )}
    </Draggable>
  );
}
```

## CardModal を作成する

- useQueryを使用してDBからデータを取得する
- Dialog
	- open
		- 開閉の状態を指定
	- onOpenChange
		- 閉じる処理を指定
	- 子要素にDialogContent コンポーネントを指定

### components/modals/card-modal/index.tsx

```tsx
'use client';

import { useQuery } from '@tanstack/react-query';

import { Dialog, DialogContent } from '@/components/ui/dialog';
import { useCardModal } from '@/hooks/use-card-modal';

import { CardWithList } from '@/types';
import { fetcher } from '@/lib/fetcher';
import Header from './header';

export default function CardModal() {
  const id = useCardModal((state) => state.id);
  const isOpen = useCardModal((state) => state.isOpen);
  const onClose = useCardModal((state) => state.onClose);

  // apiの実行
  // queryKey「card: id」でクエリをキャッシュ
  const { data: cardData } = useQuery<CardWithList>({
    queryKey: ['card', id],
    queryFn: () => fetcher(`/api/cards/${id}`),
  });

  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent>
        {!cardData ? <Header.Skelton /> : <Header data={cardData} />}
      </DialogContent>
    </Dialog>
  );
}
```