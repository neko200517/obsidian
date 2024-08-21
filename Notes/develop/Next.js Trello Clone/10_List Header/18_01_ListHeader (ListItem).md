## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       ├── _components
│   │   │   │       │   ├── list-container.tsx
│   │   │   │       │   ├── list-header.tsx
│   │   │   │       │   ├── list-item.tsx
```

## ListContainer に ListItem を組み込む

- ListItem に index, data（ListWithCards型）を渡す
- index は後述

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-container.tsx

```tsx
'use client';

import { useEffect, useState } from 'react';

import { ListWithCards } from '@/types';

import ListForm from './list-form';
import ListItem from './list-item';

interface ListContainerProps {
  boardId: string;
  data: ListWithCards[];
}

export default function ListContainer({ boardId, data }: ListContainerProps) {
  const [orderedData, setOrderedData] = useState(data);

  useEffect(() => {
    setOrderedData(data);
  }, [data]);

  return (
    <ol className='flex gap-x-3 h-full'>
      {orderedData.map((list, index) => {
        return <ListItem key={list.id} index={index} data={list} />;
      })}
      <ListForm />
      <div className='flex-shrink-0 w-1' />
    </ol>
  );
}
```

## ListItem を作成する

- ListHeader にdataを渡す

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-item.tsx

```tsx
'use client';

import { ListWithCards } from '@/types';

import ListHeader from './list-header';

interface ListItemProps {
  index: number;
  data: ListWithCards;
}

export default function ListItem({ index, data }: ListItemProps) {
  return (
    <li className='shrink-0 h-full w-[272px] select-none'>
      <div className='w-full rounded-md bg-[#f1f2f4] shadow-md pb-2'>
        <ListHeader data={data} />
      </div>
    </li>
  );
}
```