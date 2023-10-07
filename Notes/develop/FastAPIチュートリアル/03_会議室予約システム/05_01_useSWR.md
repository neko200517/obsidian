## API を呼ぶための汎用関数の作成

axios を使用したセットアップ、CRUD操作用の関数などを作成。

```ts
// api.ts

import axiosbase, { AxiosResponse } from 'axios';
import { AppConfig } from '../AppConfig';

// Axiosの設定
const axios = axiosbase.create({
  baseURL: AppConfig.baseUrl,
  headers: {
    'Content-Type': 'application/json',
  },
  responseType: 'json',
});

// GET
export const fetcher = async <T>(url: string): Promise<T> => {
  try {
    const response: AxiosResponse<T> = await axios.get(url);
    return response.data;
  } catch (error: any) {
    console.error('Error fetching data:', error.message);
    throw error;
  }
};

// POST
export const poster = async <T>(url: string, data: any): Promise<T> => {
  try {
    const response: AxiosResponse<T> = await axios.post(url, data);
    return response.data;
  } catch (error: any) {
    console.error('Error posting data:', error.message);
    throw error;
  }
};

// PUT
export const updater = async <T>(url: string, data: any): Promise<T> => {
  try {
    const response: AxiosResponse<T> = await axios.put(url, data);
    return response.data;
  } catch (error: any) {
    console.error('Error updating data:', error.message);
    throw error;
  }
};

// DELETE
export const deleter = async <T>(url: string): Promise<T> => {
  try {
    const response: AxiosResponse<T> = await axios.delete(url);
    return response.data;
  } catch (error: any) {
    console.error('Error deleting data:', error.message);
    throw error;
  }
};
```

## useSWRを使ったAPIの操作 

下記はサンプル。

useUser というユーザー情報取得APIを作成する。

```ts
import useSWR from 'swr';
import { fetcher } from '../utils/utils';
import { User } from '../models/user';

export const useUser = () => {
  const { data, error, isLoading } = useSWR<User[]>('/users', fetcher, {
    suspense: true,
  });
  return {
    data: data,
    error: error,
    isLoading: isLoading,
    isError: !data || error,
  };
};
```

使う側。useUser を使用して取得したデータを表示している。

```tsx
'use client';
import { Suspense } from 'react';
import { useUser } from '../hooks/useUser';
import { ErrorBoundary } from 'react-error-boundary';

export const UserListPage = () => {
  const UserList = () => {
    const { data } = useUser();
    if (!data) return;
    return (
      <ul>
        {data.map((user) => (
          <li key={user.user_id}>{user.username}</li>
        ))}
      </ul>
    );
  };

  return (
    <ErrorBoundary fallback={<div>Error</div>}>
      <Suspense fallback={<div>Loading...</div>}>
        <UserList />
      </Suspense>
    </ErrorBoundary>
  );
};
```

## muttate でデータの再取得

データを作成/更新して画面表示を更新するときなどに利用する。

```tsx
// 中略
mutate('/users');
// 中略
```