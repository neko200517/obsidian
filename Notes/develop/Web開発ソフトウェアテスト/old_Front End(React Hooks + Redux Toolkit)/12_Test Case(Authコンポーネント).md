## テストファイルの作成

src/testsディレクトリを作成し、Auth.test.jsファイルを作成する

```bash
mkdir src/tests
touch Auth.test.js
```

## モック部分を記述する

モックサーバーとReduxを組み合わせてテストを記述していく

```js
import React from 'react';
import { render, screen, cleanup } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import authReducer from '../features/authSlice';
import Auth from '../components/Auth';

// useNavigteをモック化
const mockNavigate = jest.fn();
jest.mock('react-router-dom', () => ({
  useNavigate: () => mockNavigate,
}));

// APIをモック化
const handlers = [
  rest.post('http://localhost:8000/api/auth/', (req, res, ctx) => {
    return res(ctx.status(200), ctx.json({ token: 'abc123' }));
  }),
  rest.post('http://localhost:8000/api/create/', (req, res, ctx) => {
    return res(ctx.status(201));
  }),
];

// モックサーバーの起動
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
```

## テストケースを書いていく

```js
describe('Auth Component Test Cases', () => {
  let store;
  // テスト開始前にReduxを使えるように準備する
  beforeEach(() => {
    store = configureStore({
      reducer: {
        auth: authReducer,
      },
    });
  });

  // 描画テスト
  it('1: Should render all the elements correctly', async () => {
    render(
      <Provider store={store}>
        <Auth />
      </Provider>
    );
    // 各要素が存在すること
    expect(screen.getByTestId('label-username')).toBeTruthy();
    expect(screen.getByTestId('input-username')).toBeTruthy();
    expect(screen.getByTestId('label-password')).toBeTruthy();
    expect(screen.getByTestId('input-password')).toBeTruthy();
    expect(screen.getByRole('button')).toBeTruthy();
    expect(screen.getByTestId('toggle-icon')).toBeTruthy();
  });

  // トグルアイコンを押下するとボタンの機能が変わること
  it('2: Should change button name by icon click', async () => {
    render(
      <Provider store={store}>
        <Auth />
      </Provider>
    );
    // デフォルトでLoginと表示されていること
    expect(screen.getByRole('button')).toHaveTextContent('Login');

    // トグルアイコンを押下するとRegisterに変更されること
    await userEvent.click(screen.getByTestId('toggle-icon'));
    expect(screen.getByRole('button')).toHaveTextContent('Register');
  });

  // ログインに成功したらMainPageに遷移すること
  it('3: Should route to MainPage when login is successful', async () => {
    render(
      <Provider store={store}>
        <Auth />
      </Provider>
    );
    // Loginボタンを押下するとSuccessfull logged in!というメッセージが表示されること
    await userEvent.click(screen.getByText('Login'));
    expect(
      await screen.findByText('Successfully logged in!')
    ).toBeInTheDocument();
    // ページ遷移の引数で/vehicleが指定されていること
    expect(mockNavigate).toHaveBeenCalledWith('/vehicle');
    // ページ遷移が1回だけ呼び出されていること
    expect(mockNavigate).toHaveBeenCalledTimes(1);
  });

  // ログインに失敗した場合、ページ遷移を行わないこと
  it('4: Should not route to MainPage with login is failed', async () => {
    // ログインに失敗するようにAPIを上書きする
    server.use(
      rest.post('http://localhost:8000/api/auth', (req, res, ctx) => {
        return res(ctx.status(400));
      })
    );
    render(
      <Provider store={store}>
        <Auth />
      </Provider>
    );
    // Loginボタンを押下するとLogin error!というメッセージが表示されること
    await userEvent.click(screen.getByText('Login'));
    expect(await screen.findByText('Login error!')).toBeInTheDocument();
    // ページ遷移が行われなかったこと
    expect(mockNavigate).toHaveBeenCalledTimes(0);
  });

  // ユーザー登録が成功したらMainPageに遷移できること
  it('5: Should output success msg when registration successded', async () => {
    render(
      <Provider store={store}>
        <Auth />
      </Provider>
    );
    // トグルボタンを押下したらボタン名がRegisterに変更されること
    await userEvent.click(screen.getByTestId('toggle-icon'));
    expect(screen.getByRole('button')).toHaveTextContent('Register');

    // 登録に成功したらSuccessfully logged in!が表示されること
    await userEvent.click(screen.getByText('Register'));
    expect(
      await screen.findByText('Successfully logged in!')
    ).toBeInTheDocument();

    // ページ遷移の引数で/vehicleが指定されていること
    expect(mockNavigate).toHaveBeenCalledWith('/vehicle');
    // ページ遷移が1回だけ呼び出されていること
    expect(mockNavigate).toHaveBeenCalledTimes(1);
  });

  // ユーザー登録が失敗したらエラーメッセージが表示され、MainPageに遷移できないこと
  it('6: Should outpu error msg when registration failed', async () => {
    // ユーザー登録に失敗するようにAPIを上書きする
    server.use(
      rest.post('http://localhost:8000/api/create', (req, res, ctx) => {
        return res(ctx.status(400));
      })
    );
    render(
      <Provider store={store}>
        <Auth />
      </Provider>
    );
    // トグルボタンを押下したらボタン名がRegisterに変更されること
    await userEvent.click(screen.getByTestId('toggle-icon'));
    expect(screen.getByRole('button')).toHaveTextContent('Register');

    // Registerボタンを押下するとRegistration error!というメッセージが表示されること
    await userEvent.click(screen.getByText('Register'));
    expect(await screen.findByText('Registration error!')).toBeInTheDocument();

    // ページ遷移が行われなかったこと
    expect(mockNavigate).toHaveBeenCalledTimes(0);
  });

  // ユーザー登録は成功するが、ログインが失敗した場合はエラーメッセージの表示とページ遷移が行われないこと
  it('7: Should output login error msg when registration success but login failed', async () => {
    // ログインに失敗するようにAPIを上書きする
    server.use(
      rest.post('http://localhost:8000/api/auth', (req, res, ctx) => {
        return res(ctx.status(400));
      })
    );
    render(
      <Provider store={store}>
        <Auth />
      </Provider>
    );
    // トグルボタンを押下したらボタン名がRegisterに変更されること
    await userEvent.click(screen.getByTestId('toggle-icon'));
    expect(screen.getByRole('button')).toHaveTextContent('Register');

    // Registerボタンを押下するとLogin error!というメッセージが表示されること
    await userEvent.click(screen.getByText('Register'));
    expect(await screen.findByText('Login error!')).toBeInTheDocument();

    // ページ遷移が行われなかったこと
    expect(mockNavigate).toHaveBeenCalledTimes(0);
  });
});
```