## Activity ページの作成

- Suspense fallback で読み込み中は Skeleton を読み込む

### app/(platform)/(dashboard)/organization/\[organizationId]/activity/page.tsx

```tsx
import { Suspense } from 'react';
import { Separator } from '@/components/ui/separator';

import Info from '../_components/info';
import ActivityList from './_components/activity-list';

export default function ActivityPage() {
  return (
    <div className='w-full'>
      <Info />
      <Separator className='my-2' />
      <Suspense fallback={<ActivityList.Skeleton />}>
        <ActivityList />
      </Suspense>
    </div>
  );
}
```

## Activity List コンポーネントの作成 

- サーバサイド処理なので直接DB処理を行っている
- css の hidden last: block で一件もデータない場合はメッセージを表示し、ある場合はメッセージを非表示にする

### app/(platform)/(dashboard)/organization/\[organizationId]/activity/\_components/activity-list.tsx

```tsx
import { auth } from '@clerk/nextjs/server';
import { redirect } from 'next/navigation';

import { db } from '@/lib/db';
import ActivityItem from '@/components/activity-item';
import { Skeleton } from '@/components/ui/skeleton';

export default async function ActivityList() {
  const { orgId } = auth();

  if (!orgId) {
    redirect('/select-org');
  }

  const auditLogs = await db.auditLog.findMany({
    where: {
      orgId,
    },
    orderBy: {
      createdAt: 'desc',
    },
  });

  return (
    <ol className='space-y-4 mt-4'>
      <p className='hidden last:block text-xs text-center text-muted-foreground'>
        No activity found inside this organization
      </p>
      {auditLogs.map((log) => (
        <ActivityItem key={log.id} data={log} />
      ))}
    </ol>
  );
}

ActivityList.Skeleton = function ActivityListSkeleton() {
  return (
    <ol className='space-y-4 mt-4'>
      <Skeleton className='w-[80%] h-14' />
      <Skeleton className='w-[50%] h-14' />
      <Skeleton className='w-[70%] h-14' />
      <Skeleton className='w-[80%] h-14' />
      <Skeleton className='w-[75%] h-14' />
    </ol>
  );
};
```