## カスタムフックの作成

```bash
mkdir hooks
touch hooks/useQueryTasks.ts
```

### Tasks用のカスタムフック 

タスク一覧の取得

#### hooks/useQueryTasks.ts 

```ts
// hooks/useQueryTasks.ts 

import axios from 'axios';
import { useQuery } from '@tanstack/react-query';
import { Task } from '../types';
import { useError } from './useError';

export const useQueryTasks = () => {
  const { switchErrorHandling } = useError();

  // タスク一覧の取得
  const getTasks = async () => {
    const { data } = await axios.get<Task[]>(
      `${import.meta.env.VITE_APP_API_URL}/tasks`,
      {
        withCredentials: true,
      }
    );
    return data;
  };

  const { data, isLoading, error } = useQuery<Task[], Error>({
    queryKey: ['tasks'],
    queryFn: getTasks,
    staleTime: Infinity, // キャッシュを持たない
  });

  if (error) {
    switchErrorHandling(error.message);
  }

  return { data, isLoading, error };
};
```