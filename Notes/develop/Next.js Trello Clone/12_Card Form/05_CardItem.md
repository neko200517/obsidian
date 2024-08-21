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

## CardItem を作成する

- DB に登録された Card を表示する

### app/(platform)/(dashboard)/board/\[boardId]/\_components/card-item.tsx

```tsx
'use client';

import { Card } from '@prisma/client';

interface CardItemProps {
  index: number;
  data: Card;
}

export default function CardItem({ index, data }: CardItemProps) {
  return (
    <div
      role='button'
      className='truncate border-2 border-transparent hover:border-black py-2 px-3 text-sm bg-white rounded-md shadow-sm'
    >
      {data.title}
    </div>
  );
}
```
