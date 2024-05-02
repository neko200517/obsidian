## src/app/contexts/StateContext.tsx

- コンポーネント間で状態を保持するためにContextを作成する

```tsx
'use client';

import { createContext, useState } from 'react';

type StateContextType = {
  selectedTask: { id: number; title: string };
  setSelectedTask: Function;
};

const defultSelectedValue = { id: 0, title: '' };

export const StateContext = createContext<StateContextType>({
  selectedTask: defultSelectedValue,
  setSelectedTask: () => {},
});

export default function StateContextProvider(props: {
  children: React.ReactNode;
}) {
  const [selectedTask, setSelectedTask] = useState(defultSelectedValue);

  return (
    <StateContext.Provider
      value={{
        selectedTask,
        setSelectedTask,
      }}
    >
      {props.children}
    </StateContext.Provider>
  );
}
```

## src/app/components/TaskList.tsx

- StateContextProvider をラップしてフォームと一覧の状態を共有する

```tsx
'use client';

import useSWR from 'swr';
import { TaskType } from '../types';
import { Task } from './Task';
import StateContextProvider from '../context/StateContext';
import TaskForm from './TaskForm';

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
    <StateContextProvider>
      <TaskForm taskCreated={mutate} />
      <ul>
        {sortedData &&
          sortedData.map((task) => (
            <Task key={task.id} task={task} taskDeleted={mutate} />
          ))}
      </ul>
    </StateContextProvider>
  );
}
```

## src/app/components/Task.tsx

- Taskコンポーネントに編集ボタンを追加する
- 編集ボタンを押下すると選択した行の状態を保存する

```tsx
'use client';

import Link from 'next/link';
import Cookie from 'universal-cookie';
import { TaskType } from '../types';

import { useContext } from 'react';
import { StateContext } from '../context/StateContext';

const cookie = new Cookie();

export function Task({
  task,
  taskDeleted,
}: {
  task: TaskType;
  taskDeleted: Function;
}) {
  const { setSelectedTask } = useContext(StateContext);

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
      <div className='float-right ml-20 mt-1 flex'>
        <svg
          xmlns='http://www.w3.org/2000/svg'
          fill='none'
          viewBox='0 0 24 24'
          stroke-width='1.5'
          stroke='currentColor'
          className='w-4 h-4 cursor-pointer'
          onClick={() => setSelectedTask(task)}
        >
          <path
            stroke-linecap='round'
            stroke-linejoin='round'
            d='m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L10.582 16.07a4.5 4.5 0 0 1-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 0 1 1.13-1.897l8.932-8.931Zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0 1 15.75 21H5.25A2.25 2.25 0 0 1 3 18.75V8.25A2.25 2.25 0 0 1 5.25 6H10'
          />
        </svg>

        <svg
          xmlns='http://www.w3.org/2000/svg'
          fill='none'
          viewBox='0 0 24 24'
          stroke-width='1.5'
          stroke='currentColor'
          className='w-4 h-4 cursor-pointer'
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

## src/app/components/TaskForm.tsx 

- タスクの編集フォームを作成する
- 状態を受けとって、選択されていなかった場合：新規作成
- 状態を受け取って、選択していた場合：更新する

```tsx
'use client';

import { FormEvent, useContext } from 'react';
import { StateContext } from '../context/StateContext';
import Cookie from 'universal-cookie';

const cookie = new Cookie();

export default function TaskForm({ taskCreated }: { taskCreated: Function }) {
  const { selectedTask, setSelectedTask } = useContext(StateContext);

  const create = async (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    await fetch(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/tasks/`, {
      method: 'POST',
      body: JSON.stringify({ title: selectedTask.title }),
      headers: {
        'Content-Type': 'application/json',
        Authorization: `JWT ${cookie.get('access_token')}`,
      },
    }).then((res) => {
      if (res.status === 401) {
        alert('JWT Token not valid');
      }
    });
    setSelectedTask({ id: 0, title: '' });
    taskCreated();
  };

  const update = async (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    await fetch(
      `${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/tasks/${selectedTask.id}/`,
      {
        method: 'PUT',
        body: JSON.stringify({ title: selectedTask.title }),
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
    setSelectedTask({ id: 0, title: '' });
    taskCreated();
  };

  return (
    <>
      <form
        className='flex mb-12'
        onSubmit={selectedTask.id === 0 ? create : update}
      >
        <input
          className='text-black px-2 py-1 mr-2'
          type='text'
          onChange={(e) =>
            setSelectedTask({ ...selectedTask, title: e.target.value })
          }
          value={selectedTask.title}
        />
        <button className='cursor-pointer bg-gray-500 px-2' type='submit'>
          {selectedTask.id === 0 ? 'CREATE' : 'UPDATE'}
        </button>
      </form>
    </>
  );
}
```