## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── board
│   │   │   │   └── [boardId]
│   │   │   │       ├── layout.tsx
│   │   │   │       └── page.tsx
```

## BoardIdLayout を作成する

- Organization に参加していない場合は選択画面に遷移する
- dbに登録されていている Board データを取得する
- 登録がなければ NotFoud 画面に遷移する
- Board データを BoardNavbar に渡す

### app/(platform)/(dashboard)/board/\[boardId]/layout.tsx

```tsx
import { auth } from '@clerk/nextjs/server';
import { notFound, redirect } from 'next/navigation';

import { db } from '@/lib/db';
import BoardNavbar from './_components/board-navbar';

export async function generateMetadata({
  params,
}: {
  params: { boardId: string };
}) {
  const { orgId } = auth();

  if (!orgId) {
    return {
      title: 'Board',
    };
  }

  const board = await db.board.findUnique({
    where: {
      id: params.boardId,
      orgId,
    },
  });

  return {
    title: board?.title || 'Board',
  };
}

export default async function BoardIdLayout({
  children,
  params,
}: {
  children: React.ReactNode;
  params: { boardId: string };
}) {
  const { orgId } = auth();

  if (!orgId) {
    redirect('/select-org');
  }

  const board = await db.board.findUnique({
    where: {
      id: params.boardId,
      orgId,
    },
  });

  if (!board) {
    notFound();
  }

  return (
    <div
      className='relative h-full bg-no-repeat bg-cover bg-center'
      style={{
        backgroundImage: `url(${board.imageFullUrl})`,
      }}
    >
      <BoardNavbar data={board} />
      <div className='absolute inset-0 bg-black/10' />
      <main className='relative pt-28 h-full'>{children}</main>
    </div>
  );
}
```

## BoardIdPage を作成する

- 仮
### app/(platform)/(dashboard)/board/\[boardId]/page.tsx

```tsx
export default function BoardIdPage() {
  return <div>Board</div>;
}
```
