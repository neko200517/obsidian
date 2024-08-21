## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       └── page.tsx
```

## BoardIdPage を修正する

- { params: xxx } でURLパスから値を取得（xxxは \[xxx]フォルダと連動）
- db.list の where 句に外部キーを指定
	- board テーブル と結合し、orgId も where 句に加える
	- include でさらに card テーブルを結合する
	- orderBy で指定したフィールドの昇順でソートする
- ListContainer に取得したデータを渡す

### app/(platform)/(dashboard)/board/\[boardId]/page.tsx

```tsx
import { redirect } from 'next/navigation';
import { auth } from '@clerk/nextjs/server';

import { db } from '@/lib/db';

import ListContainer from './_components/list-container';

interface BoardIdPageProps {
  params: {
    boardId: string;
  };
}

export default async function BoardIdPage({ params }: BoardIdPageProps) {
  const { orgId } = auth();

  if (!orgId) {
    redirect('/select-org');
  }

  const lists = await db.list.findMany({
    where: {
      boardId: params.boardId,
      board: {
        orgId,
      },
    },
    include: {
      cards: {
        orderBy: {
          order: 'asc',
        },
      },
    },
    orderBy: {
      order: 'asc',
    },
  });

  return (
    <div className='p-4 h-full overflow-x-auto'>
      <ListContainer boardId={params.boardId} data={lists} />
    </div>
  );
}
```