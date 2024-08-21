## ShadUI - Avatar の導入

- avatar の導入

```bash
npx shadcn-ui@latest add avatar
```

## ライブラリの導入 

- 日付のフォーマットに使用

```bash
npx i date-fns
```

## ActivityItem の作成

### components/activity-item.tsx

```tsx
import { format } from 'date-fns';
import { AuditLog } from '@prisma/client';

import { generateLogMessage } from '@/lib/generate-log-message';
import { Avatar, AvatarImage } from '@/components/ui/avatar';

interface ActivityItemProps {
  data: AuditLog;
}

export default function ActivityItem({ data }: ActivityItemProps) {
  return (
    <li className='flex items-center gap-x-2'>
      <Avatar className='h-8 w-8'>
        <AvatarImage src={data.userImage} />
      </Avatar>
      <div className='flex flex-col space-y-0.5'>
        <p className='text-sm text-muted-foreground'>
          <span className='font-semibold lowercase text-neutral-700'>
            {data.userName}
          </span>{' '}
          {generateLogMessage(data)}
        </p>
        <p className='text-xs text-muted-foreground'>
          {format(new Date(data.createdAt), "MMM d, yyyy 'at' h:mm a")}
        </p>
      </div>
    </li>
  );
}
```

## CardModal - Activity の作成

### components/modals/card-modal/activity.tsx

```tsx
'use client';

import { ActivityIcon } from 'lucide-react';
import { AuditLog } from '@prisma/client';

import { Skeleton } from '@/components/ui/skeleton';
import ActivityItem from '@/components/activity-item';

interface ActivityProps {
  items: AuditLog[];
}

export default function Activity({ items }: ActivityProps) {
  return (
    <div>
      <div className='flex items-start gap-x-3 w-full'>
        <ActivityIcon className='h-5 w-5 mt-0.5 text-neutral-700' />
        <div className='w-full'>
          <p className='font-semibold text-neutral-700 mb-2'>Activity</p>
          <ol className='mt-2 space-y-4'>
            {items.map((item) => (
              <ActivityItem key={item.id} data={item} />
            ))}
          </ol>
        </div>
      </div>
    </div>
  );
}

Activity.Skeleton = function ActivitySkeleton() {
  return (
    <div className='flex items-start gap-x-3 w-full'>
      <Skeleton className='h-6 w-6 bg-neutral-200' />
      <div className='w-full'>
        <Skeleton className='w-24 h-6 mb-2 bg-neutral-200' />
        <Skeleton className='w-full h-10 bg-neutral-200' />
      </div>
    </div>
  );
};
```