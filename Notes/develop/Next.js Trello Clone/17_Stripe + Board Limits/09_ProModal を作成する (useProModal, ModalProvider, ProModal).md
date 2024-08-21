## Modal の状態管理hookを作成する

### hooks/use-pro-modal.ts

```ts
import { create } from 'zustand';

type ProModalStore = {
  isOpen: boolean;
  onOpen: () => void;
  onClose: () => void;
};

export const useProModal = create<ProModalStore>((set) => ({
  isOpen: false,
  onOpen: () => set({ isOpen: true }),
  onClose: () => set({ isOpen: false }),
}));
```

## ModalProvider に ProModalを呼び出せるように追加

### components/providers/modal-provider.tsx

```tsx
'use client';

import { useEffect, useState } from 'react';

import CardModal from '@/components/modals/card-modal';
import ProModal from '@/components/modals/pro-modal';

export default function ModalProvider() {
  const [isMounted, setIsMounted] = useState(false);

  useEffect(() => {
    setIsMounted(true);
  }, []);

  if (!isMounted) {
    return null;
  }

  return (
    <>
      <CardModal />
      <ProModal />
    </>
  );
}
```

## ProModal を作成する

- 課金を促すウィンドウを表示する

### components/modals/pro-modal.tsx

```tsx
'use client';

import Image from 'next/image';
import { toast } from 'sonner';

import { useProModal } from '@/hooks/use-pro-modal';
import { Dialog, DialogContent } from '@/components/ui/dialog';
import { Button } from '@/components/ui/button';
import { useAction } from '@/hooks/use-action';
import { stripeRedirect } from '@/actions/stripe-redirect';

export default function ProModal() {
  const proModal = useProModal();

  const { execute, isLoading } = useAction(stripeRedirect, {
    onSuccess: (data) => {
      window.location.href = data;
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const onClick = () => {
    execute({});
  };

  return (
    <Dialog open={proModal.isOpen} onOpenChange={proModal.onClose}>
      <DialogContent className='max-w-md p-0 overflow-hidden'>
        <div className='aspect-video relative flex items-center justify-center'>
          <Image src='/hero.png' alt='Hero' className='object-cover' fill />
        </div>
        <div className='text-neutral-700 mx-auto space-y-6 p-6'>
          <h2 className='font-semibold text-xl'>
            Upgrade to Taskify Pro Today!
          </h2>
          <p className='text-xs font-semibold text-neutral-600'>
            Explore the best of Taskify
          </p>
          <div className='pl-3'>
            <ul className='text-sm list-disc'>
              <li>Unlimited boards</li>
              <li>Advanced checklists</li>
              <li>Admin and security features</li>
              <li>And more!</li>
            </ul>
          </div>
          <Button
            className='w-full'
            variant='primary'
            onClick={onClick}
            disabled={isLoading}
          >
            Upgrade
          </Button>
        </div>
      </DialogContent>
    </Dialog>
  );
}
```