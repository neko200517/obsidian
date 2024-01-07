## テストケースを書いていく

MainPage.test.jsファイルを作成し、テストを記述していく

```bash
touch src/tests/MainPage.test.js
```

```js
// src/tests/MainPage.test.js

import React from 'react';
import { render, screen, cleanup } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import authReducer from '../features/authSlice';
import vehicleReducer from '../features/vehicleSlice';
import MainPage from '../components/MainPage';

// useNavigteをモック化
const mockNavigate = jest.fn();
jest.mock('react-router-dom', () => ({
  useNavigate: () => mockNavigate,
}));

const handlers = [
  rest.get('http://localhost:8000/api/profile/', (req, res, ctx) => {
    return res(ctx.status(200), ctx.json({ id: 1, username: 'test user' }));
  }),
  rest.get('http://localhost:8000/api/segments', (req, res, ctx) => {
    return res(ctx.status(200), ctx.json([]));
  }),
  rest.get('http://localhost:8000/api/brands', (req, res, ctx) => {
    return res(ctx.status(200), ctx.json([]));
  }),
  rest.get('http://localhost:8000/api/vehicles', (req, res, ctx) => {
    return res(ctx.status(200), ctx.json([]));
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
  let store;
  beforeEach(() => {
    store = configureStore({
      reducer: {
        auth: authReducer,
        vehicle: vehicleReducer,
      },
    });
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
