## ShadUI - Dialog の導入

```bash
npx shadcn-ui@latest add dialog
```

## ドキュメント

https://ui.shadcn.com/docs/components/dialog

## モーダルの開閉状態の状態管理

### hooks/use-card-modal.ts

```tsx
import { create } from 'zustand';

type CardModalStore = {
  id?: string;
  isOpen: boolean;
  onOpen: (id: string) => void;
  onClose: () => void;
};

export const useCardModal = create<CardModalStore>((set) => ({
  id: undefined,
  isOpen: false,
  onOpen: (id: string) => set({ isOpen: true, id }),
  onClose: () => set({ isOpen: false, id: undefined }),
}));
```
