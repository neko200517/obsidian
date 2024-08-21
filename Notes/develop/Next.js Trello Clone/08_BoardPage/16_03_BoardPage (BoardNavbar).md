## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       ├── _components
│   │   │   │       │   ├── board-navbar.tsx
```

## BoardNavbar を作成する

- 渡された Board データを BoardTitleForm と BoardOptions に渡す

### app/(platform)/(dashboard)/board/\[boardId]/\_components/board-navbar.tsx

```tsx
import { Board } from '@prisma/client';

import BoardTitleForm from './board-title-form';
import BoardOptions from './board-options';

interface BoarNavbarProps {
  data: Board;
}

export default async function BoardNavbar({ data }: BoarNavbarProps) {
  return (
    <div className='w-full h-14 z-[40] bg-black/50 fixed top-14 flex items-center px-6 gap-x-4 text-white'>
      <BoardTitleForm data={data} />
      <div className='ml-auto'>
        <BoardOptions id={data.id} />
      </div>
    </div>
  );
}
```