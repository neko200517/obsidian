## Modal Provider の作成

- モーダルをアプリに組み込むためのプロバイダの作成
- 画面ロード時にモーダルを表示しないようにする

### components/providers/modal-provider.tsx

```tsx
'use client';

import { useEffect, useState } from 'react';

import CardModal from '@/components/modals/card-modal';

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
    </>
  );
}
```

## PlatformLayout に組み込む

- Platform配下で呼び出せるように組み込む

### app/(platform)/layout.tsx

```tsx
import { ClerkProvider } from '@clerk/nextjs';
import { Toaster } from '@/components/ui/sonner';

import ModalProvider from '@/components/providers/modal-provider';

export default function PlatformLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      <ClerkProvider afterSignOutUrl='/'>
        <Toaster />
        <ModalProvider />
	    {children}
      </ClerkProvider>
    </>
  );
}
```