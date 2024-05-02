## src/app/lib/task.ts

```tsx
import { TaskType } from '@/app/types';

// 全てのデータを取得する(日付を降順にソート)
export async function getAllTaskData(): Promise<TaskType[]> {
  const res = await fetch(
    new URL(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/list-task/`),
    { next: { revalidate: 3 } }
  );

  const data = (await res.json()) as TaskType[];

  const sortedData = [...data].sort(
    (a, b) =>
      new Date(b.created_at).getTime() - new Date(a.created_at).getTime()
  );

  return sortedData;
}

// 全てのデータのIDを取得する
export async function getAllTaskIds(): Promise<{ id: string }[]> {
  const res = await fetch(
    new URL(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/list-task/`)
  );
  const data = await res.json();

  return data.map((task: TaskType) => ({
    id: String(task.id),
  }));
}

// 特定のデータを取得する
export async function getTaskDataById(id: string): Promise<TaskType> {
  const res = await fetch(
    new URL(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/detail-task/${id}/`),
    { next: { revalidate: 3 } }
  );

  return await res.json();
}
```

## src/app/task/[id]/page.tsx 

```tsx
import { Metadata } from 'next';
import { getAllTaskData } from '../lib/task';
import { ReturnLink } from '../components/ReturnLink';
import { TaskList } from '../components/TaskList';

export const metadata: Metadata = {
  title: 'Task Page',
};

export default async function TaskPage() {
  const staticTasks = await getAllTaskData();
  return (
    <>
      <TaskList tasks={staticTasks} />
      <ReturnLink href='/main'>Back to main page</ReturnLink>
    </>
  );
}
```

## src/app/components/TaskDetail.tsx 

```tsx
'use client';

import useSWR from 'swr';
import { TaskType } from '../types';

const fetcher = (url: string) => fetch(url).then((res) => res.json());

export default function TaskDetail({
  staticTask,
  id,
}: {
  staticTask: TaskType;
  id: string;
}) {
  const { data, error } = useSWR(
    `${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/detail-task/${id}`,
    fetcher,
    {
      fallbackData: staticTask,
    }
  );

  if (error) return <div>Failed to load</div>;
  if (!data) return <div>Loading...</div>;

  return (
    <>
      <p className='mb-4'>
        {'ID : '}
        {data.id}
      </p>
      <p className='mb-4 text-xl font-bold'>{data.title}</p>
      <p className='mb-12'>{data.created_at}</p>
    </>
  );
}
```