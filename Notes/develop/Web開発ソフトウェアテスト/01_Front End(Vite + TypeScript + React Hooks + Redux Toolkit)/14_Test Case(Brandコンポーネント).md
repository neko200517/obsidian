## テストケースを書いていく

Brand.test.jsファイルを作成し、テストを記述していく

```bash
touch src/tests/Brand.test.ts
```

```ts
// src/tests/Brand.test.ts

import { render, screen, cleanup } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import vehicleReducer from '../features/vehicleSlice';
import Brand from '../components/Brand';

const handlers = [
  http.get('http://localhost:8000/api/brands/', () => {
    return HttpResponse.json([
      { id: 1, brand_name: 'Toyota' },
      { id: 2, brand_name: 'Tesla' },
    ]);
  }),
  http.post('http://localhost:8000/api/brands/', () => {
    return HttpResponse.json({ id: 3, brand_name: 'Audi' });
  }),
  http.put('http://localhost:8000/api/brands/1/', () => {
    return HttpResponse.json({ id: 1, brand_name: 'new Toyota' });
  }),
  http.put('http://localhost:8000/api/brands/2/', () => {
    return HttpResponse.json({ id: 2, brand_name: 'new Tesla' });
  }),
  http.delete('http://localhost:8000/api/brands/1/', () => {
    return HttpResponse.json();
  }),
  http.delete('http://localhost:8000/api/brands/2/', () => {
    return HttpResponse.json();
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

describe('Brand Component Test Cases', () => {
  let store: any;
  beforeEach(() => {
    // Reduxの初期化
    store = configureStore({
      reducer: {
        vehicle: vehicleReducer,
      },
    });
  });

  // コンテンツが存在していること
  it('1: Should render all the elements correctly', async () => {
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );

    // ヘッダ、テキストボックス、ボタンが存在していること
    expect(screen.getByTestId('h3-brand')).toBeTruthy();
    expect(screen.getByRole('textbox')).toBeTruthy();
    expect(screen.getByTestId('btn-post')).toBeTruthy();

    // ページを読み込んだ後Toyotaという文字列が存在していること
    expect(await screen.findByText('Toyota')).toBeInTheDocument();

    // ブランドリストが2つ存在すること
    expect(screen.getAllByRole('listitem')[0]).toBeTruthy();
    expect(screen.getAllByRole('listitem')[1]).toBeTruthy();

    // 編集ボタンが2つ存在すること
    expect(screen.getByTestId('edit-brand-1')).toBeTruthy();
    expect(screen.getByTestId('edit-brand-2')).toBeTruthy();

    // 削除ボタンが2つ存在すること
    expect(screen.getByTestId('delete-brand-1')).toBeTruthy();
    expect(screen.getByTestId('delete-brand-2')).toBeTruthy();
  });

  // 一覧取得した場合、リスト要素が表示されていること
  it('2: Should render list of brands from REST API', async () => {
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );

    // 読み込む前は前はToyotaとTeslaという文字列が存在していないこと
    expect(screen.queryByText('Toyota')).toBeNull();
    expect(screen.queryByText('Tesla')).toBeNull();

    // 読み込んだ後はToyotaとTeslaという文字列が存在していること
    expect(await screen.findByText('Toyota')).toBeInTheDocument();
    expect(await screen.findByText('Tesla')).toBeInTheDocument();

    // list-1, list-2の要素の中にToyota, Teslaという文字列が含まれていること
    expect((await screen.findByTestId('list-1')).textContent).toBe('Toyota');
    expect((await screen.findByTestId('list-2')).textContent).toBe('Tesla');
  });

  // 一覧取得に失敗した場合リスト要素が表示されていないこと
  it('3: Should not render list of brands from REST API when rejected', async () => {
    server.use(
      http.get('http://localhost:8000/api/brands', () => {
        return HttpResponse.error();
      })
    );
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );
    // 読み込み前に一覧の文字列が存在しないこと
    expect(screen.queryByText('Toyota')).toBeNull();
    expect(screen.queryByText('Tesla')).toBeNull();

    // 読み込んだ後にGet error!が表示されていること
    expect(await screen.findByText('Get error!')).toBeInTheDocument();

    // 読み込んだ後も一覧の文字列が存在しないこと
    expect(screen.queryByText('Toyota')).toBeNull();
    expect(screen.queryByText('Tesla')).toBeNull();
  });

  // 新規登録したら一覧に要素が表示されること
  it('4: Should add new brand and also to the list', async () => {
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );

    // 新規登録前はAudiが存在しないこと
    expect(await screen.queryByText('Audi')).toBeNull();

    // 入力とクリックイベントをシミュレートする
    const inputValue = screen.getByPlaceholderText('new brand name');
    await userEvent.type(inputValue, 'Audi');
    await userEvent.click(screen.getByTestId('btn-post'));

    // 新規登録後はAudiが存在すること
    expect(await screen.findByText('Audi')).toBeInTheDocument();
  });

  // データを削除できること（id 1）
  it('5: Should delete brand(id 1) and also from list', async () => {
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );
    // 読み込む前はToyotaとTeslaが存在しないこと
    expect(screen.queryByText('Toyota')).toBeNull();
    expect(screen.queryByText('Tesla')).toBeNull();

    // 削除前はToyotaとTeslaが表示されていること
    expect(await screen.findByText('Toyota')).toBeInTheDocument();
    expect(await screen.findByText('Tesla')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Toyota');
    expect(screen.getByTestId('list-2').textContent).toBe('Tesla');

    // 1番目の削除ボタンをクリックすると削除メッセージが表示され、データが削除されること
    await userEvent.click(screen.getByTestId('delete-brand-1'));
    expect(await screen.findByText('Deleted in brand!')).toBeInTheDocument();
    expect(screen.queryByText('Toyota')).toBeNull();
  });

  // データを削除できること（id 2）
  it('6: Should delete brand(id 2) and also from list', async () => {
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );
    // 読み込む前はToyotaとTeslaが存在しないこと
    expect(screen.queryByText('Toyota')).toBeNull();
    expect(screen.queryByText('Tesla')).toBeNull();

    // 削除前はToyotaとTeslaが表示されていること
    expect(await screen.findByText('Toyota')).toBeInTheDocument();
    expect(await screen.findByText('Tesla')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Toyota');
    expect(screen.getByTestId('list-2').textContent).toBe('Tesla');

    // 2番目の削除ボタンをクリックすると削除メッセージが表示され、データが削除されること
    await userEvent.click(screen.getByTestId('delete-brand-2'));
    expect(await screen.findByText('Deleted in brand!')).toBeInTheDocument();
    expect(screen.queryByText('Tesla')).toBeNull();
  });

  // 更新ボタンを押下するとデータが更新されること（id 1）
  it('7: Should delete brand(id 1) and also in the list', async () => {
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );
    // 読み込む前はToyotaとTeslaが存在しないこと
    expect(screen.queryByText('Toyota')).toBeNull();
    expect(screen.queryByText('Tesla')).toBeNull();

    // 読み込んだ後はToyotaとTeslaが表示されていること
    expect(await screen.findByText('Toyota')).toBeInTheDocument();
    expect(await screen.findByText('Tesla')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Toyota');
    expect(screen.getByTestId('list-2').textContent).toBe('Tesla');

    // 1番目のEditボタンを押下して更新ボタンを押下するとデータが更新されること
    const inputElement = screen.getByPlaceholderText('new brand name');
    await userEvent.click(screen.getByTestId('edit-brand-1'));
    await userEvent.clear(inputElement);
    await userEvent.type(inputElement, 'new Toyota');
    await userEvent.click(screen.getByTestId('btn-post'));
    expect(await screen.findByText('Updated in brand!')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('new Toyota');
  });

  // 更新ボタンを押下するとデータが更新されること（id 2）
  it('8: Should delete brand(id 2) and also in the list', async () => {
    render(
      <Provider store={store}>
        <Brand />
      </Provider>
    );
    // 読み込む前はToyotaとTeslaが存在しないこと
    expect(screen.queryByText('Toyota')).toBeNull();
    expect(screen.queryByText('Tesla')).toBeNull();

    // 読み込んだ後はToyotaとTeslaが表示されていること
    expect(await screen.findByText('Toyota')).toBeInTheDocument();
    expect(await screen.findByText('Tesla')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Toyota');
    expect(screen.getByTestId('list-2').textContent).toBe('Tesla');

    // 2番目のEditボタンを押下して更新ボタンを押下するとデータが更新されること
    const inputElement = screen.getByPlaceholderText('new brand name');
    await userEvent.click(screen.getByTestId('edit-brand-2'));
    await userEvent.clear(inputElement);
    await userEvent.type(inputElement, 'new Tesla');
    await userEvent.click(screen.getByTestId('btn-post'));
    expect(await screen.findByText('Updated in brand!')).toBeInTheDocument();
    expect(screen.getByTestId('list-2').textContent).toBe('new Tesla');
  });
});
```
