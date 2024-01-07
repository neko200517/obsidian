## Todo.tsxの実装

タスク一覧の表示、入力フォーム、登録・更新ボタン、ログアウト

```tsx
// components/Todo.tsx

import { FormEvent } from 'react';
import { useQueryClient } from '@tanstack/react-query';
import {
  ArrowRightStartOnRectangleIcon,
  ShieldCheckIcon,
} from '@heroicons/react/24/solid';
import useStore from '../store';
import { useQueryTasks } from '../hooks/useQueryTasks';
import { useMutateTask } from '../hooks/useMutateTask';
import { useMutateAuth } from '../hooks/useMutateAuth';
import { TaskItem } from './TaskItem';

export const Todo = () => {
  const queryClient = useQueryClient();
  const { editedTask } = useStore();
  const updateTask = useStore((state) => state.updateEditedTask);
  const { data, isLoading } = useQueryTasks();
  const { createTaskMutation, updateTaskMutation } = useMutateTask();
  const { logoutMutation } = useMutateAuth();

  // ログアウト
  const logout = async () => {
    await logoutMutation.mutateAsync();
    // キャッシュクリア
    queryClient.removeQueries({ queryKey: ['tasks'] });
  };

  const submitTaskHander = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    if (editedTask.id === 0) {
      // 未選択の場合
      createTaskMutation.mutate({
        title: editedTask.title,
      });
    } else {
      // 鉛筆アイコンを押下後
      updateTaskMutation.mutate(editedTask);
    }
  };

  return (
    <div className='flex justify-center items-center flex-col min-h-screen text-gray-600  font-mono'>
      {/* タイトル */}
      <div className='flex items-center my-3'>
        <ShieldCheckIcon className='h-8 w-8 mr-3 text-indigo-500 cursor-pointer' />
        <span className='text-center text-3xl font-extrabold'>
          Task Manager
        </span>
      </div>

      {/* ログアウトアイコン */}
      <ArrowRightStartOnRectangleIcon
        className='h-6 w-6 my-6 text-blue-500 cursor-pointer'
        onClick={logout}
      />

      {/* フォーム */}
      <form onSubmit={submitTaskHander}>
        {/* タイトル入力 */}
        <input
          className='mb-3 mr-3 px-3 py-2 border border-gray-300'
          placeholder='title ?'
          type='text'
          onChange={(e) => updateTask({ ...editedTask, title: e.target.value })}
          value={editedTask.title || ''}
        />
        {/* ボタン */}
        <button
          className='disabled:opacity-40 py-2 px-3 text-white bg-indigo-600 rounded'
          disabled={!editedTask.title}
        >
          {editedTask.id === 0 ? 'Create' : 'Update'}
        </button>
      </form>

      {isLoading ? (
        <p>Loading...</p>
      ) : (
        // タスク一覧
        <ul className='my-5'>
          {data?.map((task) => (
            <TaskItem key={task.id} id={task.id} title={task.title} />
          ))}
        </ul>
      )}
    </div>
  );
};
```
