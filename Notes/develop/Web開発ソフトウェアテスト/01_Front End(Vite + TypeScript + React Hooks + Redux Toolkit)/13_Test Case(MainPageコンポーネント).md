---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - vite
  - typescript
  - react-hooks
  - redux-toolkit
  - test
aliases:
  - <% tp.file.title %>
---

## テストケースを書いていく

MainPage.test.js ファイルを作成し、テストを記述していく

```bash
touch src/tests/MainPage.test.ts
```

```js
// src/tests/MainPage.test.ts

import { render, screen, cleanup } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import authReducer from '../features/authSlice';
import vehicleReducer from '../features/vehicleSlice';
import MainPage from '../components/MainPage';

// useNavigteをモック化
const mockNavigate = vi.fn();
vi.mock('react-router-dom', () => ({
  useNavigate: () => mockNavigate,
}));

const handlers = [
  http.get('http://localhost:8000/api/profile/', () => {
    return HttpResponse.json({ id: 1, username: 'test user' });
  }),
  http.get('http://localhost:8000/api/segments', () => {
    return HttpResponse.json([]);
  }),
  http.get('http://localhost:8000/api/brands', () => {
    return HttpResponse.json([]);
  }),
  http.get('http://localhost:8000/api/vehicles', () => {
    return HttpResponse.json([]);
  }),
];

const server = setupServer(...handlers);

beforeAll(() => {
  server.listen();
});
afterEach(() => {
  server.resetHandlers();
  cleanup();
});
afterAll(() => {
  server.close();
});

describe('MainPage Component Test Cases', () => {
  let store: any;
  beforeEach(() => {
    // Reduxの初期化
    store = configureStore({
      reducer: {
        auth: authReducer,
        vehicle: vehicleReducer,
      },
    });
    // モック関数の初期化
    mockNavigate.mockClear();
  });

  // 必要なコンテンツが表示されていること
  it('1: Should render all the elements correctly', async () => {
    render(
      <Provider store={store}>
        <MainPage />
      </Provider>
    );
    // span-titleが存在すること
    expect(screen.getByTestId('span-title')).toBeTruthy();

    // btn-logoutが存在すること
    expect(screen.getByTestId('btn-logout')).toBeTruthy();
  });

  // ログアウトを押下するとログイン画面に遷移すること
  it('2: Should route to Auth page when logout button pressed', async () => {
    render(
      <Provider store={store}>
        <MainPage />
      </Provider>
    );
    await userEvent.click(screen.getByTestId('btn-logout'));

    // ページ遷移の引数で/が指定されていること
    expect(mockNavigate).toHaveBeenCalledWith('/');

    // ページ遷移が1回だけ呼び出されていること
    expect(mockNavigate).toHaveBeenCalledTimes(1);
  });

  // ユーザー名が表示されていること
  it('3: Should render logged in user name', async () => {
    render(
      <Provider store={store}>
        <MainPage />
      </Provider>
    );
    // ユーザー情報読み込み前はユーザー名が表示されていないこと
    expect(screen.queryByText('test user')).toBeNull();

    // ユーザー情報読み込み後はユーザー名が表示されていること
    expect(await screen.findByText('test user')).toBeInTheDocument();
  });
});
```
