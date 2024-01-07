## カスタムフックの作成

```bash
mkdir hooks
touch hooks/useMutateAuth.ts
```

### Auth用のカスタムフック 

サインアップ、ログイン、ログアウト用のカスタムフック。

#### hooks/useMutateAuth.ts 

```ts
// hooks/useMutateAuth.ts 

import axios from 'axios';
import { useNavigate } from 'react-router-dom';
import { useMutation } from '@tanstack/react-query';
import useStore from '../store';
import { Credentials } from '../types';
import { useError } from './useError';

export const useMutateAuth = () => {
  const navigate = useNavigate();
  const resetEditedTask = useStore((state) => state.resetEditedTask);
  const { switchErrorHandling } = useError();

  // ログイン
  //  useMutation: データ更新
  //  mutationFnに関数登録
  //  使用方法：loginMutation.mutate(user)
  const loginMutation = useMutation({
    mutationFn: async (user: Credentials) =>
      await axios.post(`${import.meta.env.VITE_APP_API_URL}/login`, user),
    onSuccess: () => {
      navigate('/todo');
    },
    onError: (err: any) => {
      if (err.response.data.message) {
        switchErrorHandling(err.response.data.message);
      } else {
        switchErrorHandling(err.response.data);
      }
    },
  });

  // サインアップ
  const registerMutation = useMutation({
    mutationFn: async (user: Credentials) =>
      await axios.post(`${import.meta.env.VITE_APP_API_URL}/signup`, user),
    onError: (err: any) => {
      if (err.response.data.message) {
        switchErrorHandling(err.response.data.message);
      } else {
        switchErrorHandling(err.response.data);
      }
    },
  });

  // ログアウト
  const logoutMutation = useMutation({
    mutationFn: async () =>
      await axios.post(`${import.meta.env.VITE_APP_API_URL}/logout`),
    onSuccess: () => {
      resetEditedTask();
      navigate('/');
    },
    onError: (err: any) => {
      if (err.response.data.message) {
        switchErrorHandling(err.response.data.message);
      } else {
        switchErrorHandling(err.response.data);
      }
    },
  });

  return { loginMutation, registerMutation, logoutMutation };
};
```