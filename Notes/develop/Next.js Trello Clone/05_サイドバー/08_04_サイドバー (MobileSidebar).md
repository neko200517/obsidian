## ライブラリをインストール

```bash
npm i zustand
```

## ShadUI - Sheet のインストール

```bash
npx shadcn-ui@latest add sheet
```

## ファイル構成

```tsx
.
├── app
│    └── (dashboard)
│         └── _components
│             └── mobile-sidebar.tsx
├── hooks
│   └── use-mobile-sidebar.ts
```

## useMobileSidebar を作成する

メニューの開閉状態を管理するカスタムフック

### hooks/use-mobile-sidebar.ts

```ts
import { create } from 'zustand';

type MobileSidebarStore = {
  isOpen: boolean;
  onOpen: () => void;
  onClose: () => void;
};

export const useMobileSidebar = create<MobileSidebarStore>((set) => ({
  isOpen: false,
  onOpen: () => set({ isOpen: true }),
  onClose: () => set({ isOpen: false }),
}));
```

## MobileSidebar を作成する

- モバイルサイズのみ表示されるハンバーガーメニュー
- アイコンを押下するとメニューがスライドする

### app/(platform)/(dashboard)/\_components/mobile-sidebar.tsx

```tsx
'use client';

import { useEffect, useState } from 'react';
import { usePathname } from 'next/navigation';
import { Menu } from 'lucide-react';

import { Button } from '@/components/ui/button';
import { Sheet, SheetContent } from '@/components/ui/sheet';

import { useMobileSidebar } from '@/hooks/use-mobile-sidebar';
import Sidebar from './sidebar';

export default function MobileSidebar() {
  const pathname = usePathname();
  const [isMounted, setIsMounted] = useState(false);

  const onOpen = useMobileSidebar((state) => state.onOpen);
  const onClose = useMobileSidebar((state) => state.onClose);
  const isOpen = useMobileSidebar((state) => state.isOpen);

  useEffect(() => {
    setIsMounted(true);
  }, []);

  useEffect(() => {
    onClose();
  }, [pathname, onClose]);

  if (!isMounted) {
    return null;
  }

  return (
    <>
      <Button
        onClick={onOpen}
        className='block md:hidden mr-2'
        variant='ghost'
        size='sm'
      >
        <Menu className='h-4 w-4' />
      </Button>
      <Sheet open={isOpen} onOpenChange={onClose}>
        <SheetContent side='left' className='p-2 pt-10'>
          <Sidebar storageKey='t-sidebar-mobile-state' />
        </SheetContent>
      </Sheet>
    </>
  );
}
```

## Navar に MobileSidebar を 組み込む

### app/(platform)/(dashboard)/\_components/navbar.tsx

```tsx
//...
export default function Navbar() {
  return (
    <nav className='fixed z-50 top-0 px-4 w-full h-14 border-b shadow-sm bg-white flex items-center'>
      <MobileSidebar />
      //...
```