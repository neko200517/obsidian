## 概要

リアルタイムにデータが更新されるが、あらかじめプリレンダリングされた情報を与えることでSSO対策する組み合わせを実装する。

## src/app/types.ts

- TaskTypeの定義

```ts
export type PostType = {
  id: number;
  title: string;
  content: string;
  created_at: string;
};

export type TaskType = {
  id: number;
  title: string;
  created_at: string;
};
```

## src/app/lib/task.ts 

- タスク一覧を取得
- ISR で データが更新された場合 html が再生成される

```ts
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
```

## src/app/task/page.tsx

- 事前に staticTasks にサーバサイドで取得し、コンポーネントに渡す

```tsx
import { getAllTaskData } from '../lib/task';
import { ReturnLink } from '../components/ReturnLink';
import { TaskList } from '../components/TaskList';

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

## src/app/components/TaksList.tsx

- データが更新されたら即時反映される
- 事前に取得したリストを fallbackData に与えることで初期値として値を渡すことができる

```tsx
'use client';

import useSWR from 'swr';
import { TaskType } from '../types';
import { Task } from './Task';

const fetcher = (url: string) => fetch(url).then((res) => res.json());
const apiUrl = `${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/list-task/`;

export function TaskList({ tasks }: { tasks: TaskType[] }) {
  const { data, error } = useSWR(apiUrl, fetcher, { fallbackData: tasks });

  if (error) return <div>Failed to load</div>;
  if (!data) return <div>Loading...</div>;

  const sortedData = [...data].sort(
    (a, b) =>
      new Date(b.created_at).getTime() - new Date(a.created_at).getTime()
  );
  return (
    <ul>
      {sortedData &&
        sortedData.map((task) => <Task key={task.id} task={task} />)}
    </ul>
  );
}
```