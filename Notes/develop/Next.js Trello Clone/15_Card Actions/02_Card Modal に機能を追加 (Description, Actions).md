## CardModal に機能を追加

 - Description を追加 
 - Actions を追加

### components/modals/card-modal/index.tsx

```tsx
'use client';

import { useQuery } from '@tanstack/react-query';

import { Dialog, DialogContent } from '@/components/ui/dialog';
import { useCardModal } from '@/hooks/use-card-modal';

import { CardWithList } from '@/types';
import { fetcher } from '@/lib/fetcher';
import Header from './header';
import Description from './description';
import Actions from './actions';

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

        <div className='grid grid-cols-1 md:grid-cols-4 md:gap-4'>
          <div className='col-span-3'>
            <div className='w-full space-y-6'>
              {!cardData ? (
                <Description.Skelelton />
              ) : (
                <Description data={cardData} />
              )}
            </div>
          </div>
          <div>
            {!cardData ? <Actions.Skeleton /> : <Actions data={cardData} />}
          </div>
        </div>
      </DialogContent>
    </Dialog>
  );
}
```