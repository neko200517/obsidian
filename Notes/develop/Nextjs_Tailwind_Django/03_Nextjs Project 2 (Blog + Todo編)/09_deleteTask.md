## src/app/components/TaskList.tsx

タスク一覧画面に useSWR の mutate を渡して即時反映可能にする

```tsx
'use client';

import useSWR from 'swr';
import { TaskType } from '../types';
import { Task } from './Task';

const fetcher = (url: string) => fetch(url).then((res) => res.json());
const apiUrl = `${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/list-task/`;

export function TaskList({ tasks }: { tasks: TaskType[] }) {
  const { data, error, mutate } = useSWR(apiUrl, fetcher, {
    fallbackData: tasks,
  });

  if (error) return <div>Failed to load</div>;
  if (!data) return <div>Loading...</div>;

  const sortedData = [...data].sort(
    (a, b) =>
      new Date(b.created_at).getTime() - new Date(a.created_at).getTime()
  );
  return (
    <ul>
      {sortedData &&
        sortedData.map((task) => (
          <Task key={task.id} task={task} taskDeleted={mutate} />
        ))}
    </ul>
  );
}
```

## src/app/components/Task.tsx

- ゴミ箱アイコンを押下するとデータが削除する
- Authorization ヘッダに cookie の情報を渡す
- JWT トークンの有効期限が切れたらエラーメッセージを表示
- ボタンを押下したら 渡された taskDeleted 関数を呼び出して親コンポーネントの mutate を実行する

```tsx
'use client';

import Link from 'next/link';
import { TaskType } from '../types';
import Cookie from 'universal-cookie';

const cookie = new Cookie();

export function Task({
  task,
  taskDeleted,
}: {
  task: TaskType;
  taskDeleted: Function;
}) {
  const deleteTask = async () => {
    await fetch(
      `${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/tasks/${task.id}/`,
      {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json',
          Authorization: `JWT ${cookie.get('access_token')}`,
        },
      }
    ).then((res) => {
      if (res.status === 401) {
        alert('JWT Token not valid');
      }
    });
    taskDeleted();
  };

  return (
    <div>
      <span>{task.id}</span>
      {' : '}
      <Link href={`/task/${task.id}`}>
        <span className='cursor-pointer text-white border-b border-gray-500 hover:bg-gray-600'>
          {task.title}
        </span>
      </Link>
      <div className='float-right ml-20 mt-1'>
        <svg
          xmlns='http://www.w3.org/2000/svg'
          fill='none'
          viewBox='0 0 24 24'
          stroke-width='1.5'
          stroke='currentColor'
          className='w-4 h-4 mr-2 cursor-pointer'
          onClick={deleteTask}
        >
          <path
            stroke-linecap='round'
            stroke-linejoin='round'
            d='m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0'
          />
        </svg>
      </div>
    </div>
  );
}
```