## main.tsx

main.tsxにreact-query用のコンポーネントプロバイダーをラップしてシステム全体で利用可能にする。

```ts
// main.tsx

import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App.tsx';
import './index.css';

// React-Query
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

const queryClient = new QueryClient({});

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <App />
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  </React.StrictMode>
);
```

## Components

AuthコンポーネントとTodoコンポーネントをあらかじめ作成する。

```bash
mkdir src/components
touch src/components/Auth.tsx 
touch src/components/Todo.tsx
```
### Auth.tsx

```tsx
// src/components/Auth.tsx 

export const Auth = () => {
  return <div>Auth</div>;
};
```
### Todo.tsx

```ts
// src/components/Todo.tsx 

export const Todo = () => {
  return <div>Todo</div>;
};
```

## App.tsx 

src/App.tsxにルーティングとトークン取得の処理を実装する。

```tsx
// src/App.tsx

import { useEffect } from 'react';
import { BrowserRouter, Route, Routes } from 'react-router-dom';
import axios from 'axios';
import { Auth } from './components/Auth';
import { Todo } from './components/Todo';
import { CsrfToken } from './types';

function App() {
  useEffect(() => {
    // Credentialsを有効
    axios.defaults.withCredentials = true;

    // トークンの取得
    const getCsrfToken = async () => {
      const { data } = await axios.get<CsrfToken>(
        `${import.meta.env.VITE_APP_API_URL}/csrf`
      );

      // X-CSRF-Tokenにトークン情報を設定
      axios.defaults.headers.common['X-CSRF-Token'] = data.csrf_token;
    };
    getCsrfToken();
  }, []);

  return (
    <BrowserRouter>
      <Routes>
        <Route path='/' element={<Auth />} />
        <Route path='/todo' element={<Todo />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```
