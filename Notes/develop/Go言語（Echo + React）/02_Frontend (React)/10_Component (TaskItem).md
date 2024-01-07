## TaskItem.tsxの実装

タスク名、編集ボタン、削除ボタンを表示するリストアイテム要素

```tsx
// components/TaskItem.tsx

import { FC, memo } from 'react';
import { PencilIcon, TrashIcon } from '@heroicons/react/24/solid';
import useStore from '../store';
import { Task } from '../types';
import { useMutateTask } from '../hooks/useMutateTask';

// props id: タスクのID, title: タスクのタイトル
const TaskItemMemo: FC<Omit<Task, 'created_at' | 'updated_at'>> = ({
  id,
  title,
}) => {
  const updateTask = useStore((state) => state.updateEditedTask);
  const { deleteTaskMutation } = useMutateTask();

  return (
    <li className='my-3'>
      <span className='font-bold'>{title}</span>
      <div className='flex float-right ml-20'>
        <PencilIcon
          className='h-5 w-5 mx-1 text-blue-500 cursor-pointer'
          onClick={() => {
            // クリックしたらzustandの更新
            // 親コンポーネントのフォーム変更
            updateTask({ id: id, title: title });
          }}
        />
        <TrashIcon
          className='h-5 w-5 mx-1 text-blue-500 cursor-pointer'
          onClick={() => {
            // タスクの削除
            deleteTaskMutation.mutate(id);
          }}
        />
      </div>
    </li>
  );
};

// コンポーネントのメモ化
export const TaskItem = memo(TaskItemMemo);
```
