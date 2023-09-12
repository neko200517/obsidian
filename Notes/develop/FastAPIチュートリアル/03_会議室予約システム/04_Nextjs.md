## プロジェクトの作成

```sh
npx create-next-app@latest
```

質問されるが、すべてデフォルトで作成する

## 通信ライブラリのインストール

```sh
npm i -D axios
npm i -D swr
```

## 時間操作のライブラリ

```sh
npm i -D dayjs
```

## 文字列をhtmlに変換するライブラリ

```sh
npm i -D html-react-parser
```

## エラーバウンダリー

```sh
npm i -D react-error-boundary
```

## tailwindcssを設定順をソートしてくれるライブラリ 

```sh
npm i -D prettier-plugin-tailwindcss
```

プロジェクトのルートに .prettierrc を作成し、以下の設定で保存する

```json
{
  "plugins": ["prettier-plugin-tailwindcss"], // プラグインの有効化
  
  // 以下は保存時にシングルクォートの変換がうまく動作しないときに追記する
  "singleQuote": true,
  "jsxSingleQuote": true
}
```

## axiosにルートディレクトリなどを設定 

fetcher.ts を作成し、以下の内容で保存

```ts
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

## Suspense 

非同期API取得中のローディングを実現するためにSuspenseコンポーネントを使用している。
useSWRと組み合わせるには useSWR 利用時に ``` {suspense: true} ``` オプションを付与する。
ただしコンポーネント内にデータフェッチを閉じ込める必要があるため state が頻繁に変化するページでは通信が頻繁に発生するため注意。

```tsx
	// 省略
      <Suspense fallback={<div>Loading...</div>}>
        <UserList />
      </Suspense>
	//省略
```

## ErrorBoundary 

Suspenseコンポーネントと非同期処理の例外処理をフックするために必要。ErrorBoundary で囲んだ子コンポーネントで発生したすべての例外処理をフックして fallback で代替画面を表示することができる。

```tsx
  // 省略
  return (
    <ErrorBoundary fallback={<div>Error</div>}>
      <Suspense fallback={<div>Loading...</div>}>
        <UserList />
      </Suspense>
    </ErrorBoundary>
  );
```