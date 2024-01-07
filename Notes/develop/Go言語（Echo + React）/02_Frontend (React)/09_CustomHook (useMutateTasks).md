## カスタムフックの作成

```bash
mkdir hooks
touch hooks/useMutateTasks.ts
```

### Tasks用のカスタムフック 

タスクの新規作成、更新、削除

#### hooks/useMutateTasks.ts 

```ts
// hooks/useMutateTasks.ts 

import axios from 'axios';
import { useQueryClient, useMutation } from '@tanstack/react-query';
import { Task } from '../types';
import useStore from '../store';
import { useError } from './useError';

export const useMutateTask = () => {
  const queryClient = useQueryClient();
  const { switchErrorHandling } = useError();
  const resetEditedTask = useStore((state) => state.resetEditedTask);

  // 新規作成
  const createTaskMutation = useMutation({
    mutationFn: (task: Omit<Task, 'id' | 'created_at' | 'updated_at'>) =>
      // task: 追加するデータ（titleのみ）
      axios.post<Task>(`${import.meta.env.VITE_APP_API_URL}/tasks`, task),
    onSuccess: (res) => {
      // res: レスポンス

      // getQueryData ... キャッシュtasksのデータを取得
      const previosTasks = queryClient.getQueryData<Task[]>(['tasks']);
      if (previosTasks) {
        // setQueryData ... キャッシュtasksに取得したデータを追加
        queryClient.setQueryData(['tasks'], [...previosTasks, res.data]);
      }
      resetEditedTask();
    },
    onError: (err: any) => {
      if (err.response.data.message) {
        switchErrorHandling(err.response.data.message);
      } else {
        switchErrorHandling(err.response.data);
      }
    },
  });

  // 更新
  const updateTaskMutation = useMutation({
    mutationFn: (task: Omit<Task, 'created_at' | 'updated_at'>) =>
      // task: 対象のデータ（id, title）
      axios.put<Task>(`${import.meta.env.VITE_APP_API_URL}/tasks/${task.id}`, {
        title: task.title,
      }),
    onSuccess: (res, valiables) => {
      // res: レスポンス
      // valiables: 対象のデータ（id, title）
      const previosTasks = queryClient.getQueryData<Task[]>(['tasks']);
      if (previosTasks) {
        queryClient.setQueryData<Task[]>(
          ['tasks'],
          previosTasks.map((task) =>
            task.id === valiables.id ? res.data : task
          )
        );
      }
      resetEditedTask();
    },
    onError: (err: any) => {
      if (err.response.data.message) {
        switchErrorHandling(err.response.data.message);
      } else {
        switchErrorHandling(err.response.data);
      }
    },
  });

  // 削除
  const deleteTaskMutation = useMutation({
    mutationFn: (id: number) =>
      // id: 対象のid
      axios.delete(`${import.meta.env.VITE_APP_API_URL}/tasks/${id}`),
    onSuccess: (_, valiables) => {
      // valiables: 対象のid
      const previosTasks = queryClient.getQueryData<Task[]>(['tasks']);
      if (previosTasks) {
        queryClient.setQueryData<Task[]>(
          ['tasks'],
          previosTasks.filter((task) => task.id !== valiables)
        );
      }
      resetEditedTask();
    },
    onError: (err: any) => {
      if (err.response.data.message) {
        switchErrorHandling(err.response.data.message);
      } else {
        switchErrorHandling(err.response.data);
      }
    },
  });

  return { createTaskMutation, updateTaskMutation, deleteTaskMutation };
};
```