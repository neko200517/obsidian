## テストケースを書いていく

Seglment.test.jsファイルを作成し、テストを記述していく

```bash
touch src/tests/Segment.test.js
```

```js

// src/tests/Segment.test.js

import React from 'react';
import { render, screen, cleanup } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import vehicleReducer from '../features/vehicleSlice';
import Segment from '../components/Segment';

const handlers = [
  rest.get('http://localhost:8000/api/segments/', (req, res, ctx) => {
    return res(
      ctx.status(200),
      ctx.json([
        { id: 1, segment_name: 'Sedan' },
        { id: 2, segment_name: 'SUV' },
      ])
    );
  }),
  rest.post('http://localhost:8000/api/segments/', (req, res, ctx) => {
    return res(ctx.status(201), ctx.json({ id: 3, segment_name: 'Sports' }));
  }),
  rest.put('http://localhost:8000/api/segments/1/', (req, res, ctx) => {
    return res(ctx.status(200), ctx.json({ id: 1, segment_name: 'new Sedan' }));
  }),
  rest.put('http://localhost:8000/api/segments/2/', (req, res, ctx) => {
    return res(ctx.status(200), ctx.json({ id: 2, segment_name: 'new SUV' }));
  }),
  rest.delete('http://localhost:8000/api/segments/1/', (req, res, ctx) => {
    return res(ctx.status(200));
  }),
  rest.delete('http://localhost:8000/api/segments/2/', (req, res, ctx) => {
    return res(ctx.status(200));
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

describe('Segment Component Test Cases', () => {
  let store;
  beforeEach(() => {
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
        <Segment />
      </Provider>
    );

    // ヘッダ、テキストボックス、ボタンが存在していること
    expect(screen.getByTestId('h3-segment')).toBeTruthy();
    expect(screen.getByRole('textbox')).toBeTruthy();
    expect(screen.getByTestId('btn-post')).toBeTruthy();

    // ページを読み込んだ後Sedanという文字列が存在していること
    expect(await screen.findByText('Sedan')).toBeInTheDocument();

    // リストが2つ存在すること
    expect(screen.getAllByRole('listitem')[0]).toBeTruthy();
    expect(screen.getAllByRole('listitem')[1]).toBeTruthy();

    // 編集ボタンが2つ存在すること
    expect(screen.getByTestId('edit-seg-1')).toBeTruthy();
    expect(screen.getByTestId('edit-seg-2')).toBeTruthy();

    // 削除ボタンが2つ存在すること
    expect(screen.getByTestId('delete-seg-1')).toBeTruthy();
    expect(screen.getByTestId('delete-seg-2')).toBeTruthy();
  });

  // 一覧取得した場合、リスト要素が表示されていること
  it('2: Should render list of segments from REST API', async () => {
    render(
      <Provider store={store}>
        <Segment />
      </Provider>
    );

    // 読み込む前は前はSedanとSUVという文字列が存在していないこと
    expect(screen.queryByText('Sedan')).toBeNull();
    expect(screen.queryByText('SUV')).toBeNull();

    // 読み込んだ後はSedanとSUVという文字列が存在していること
    expect(await screen.findByText('Sedan')).toBeInTheDocument();
    expect(await screen.findByText('SUV')).toBeInTheDocument();

    // list-1, list-2の要素の中にSedan, SUVという文字列が含まれていること
    expect((await screen.findByTestId('list-1')).textContent).toBe('Sedan');
    expect((await screen.findByTestId('list-2')).textContent).toBe('SUV');
  });

  // 一覧取得に失敗した場合リスト要素が表示されていないこと
  it('3: Should not render list of segments from REST API when rejected', async () => {
    server.use(
      rest.get('http://localhost:8000/api/segments', (req, res, ctx) => {
        return rex(ctx.status(400));
      })
    );
    render(
      <Provider store={store}>
        <Segment />
      </Provider>
    );
    // 読み込み前に一覧の文字列が存在しないこと
    expect(screen.queryByText('Sedan')).toBeNull();
    expect(screen.queryByText('SUV')).toBeNull();

    // 読み込んだ後にGet error!が表示されていること
    expect(await screen.findByText('Get error!')).toBeInTheDocument();

    // 読み込んだ後も一覧の文字列が存在しないこと
    expect(screen.queryByText('Sedan')).toBeNull();
    expect(screen.queryByText('SUV')).toBeNull();
  });

  // 新規登録したら一覧に要素が表示されること
  it('4: Should add new segment and also to the list', async () => {
    render(
      <Provider store={store}>
        <Segment />
      </Provider>
    );

    // 新規登録前はSportsが存在しないこと
    expect(await screen.queryByText('Sports')).toBeNull();

    // 入力とクリックイベントをシミュレートする
    const inputValue = screen.getByPlaceholderText('new segment name');
    await userEvent.type(inputValue, 'Sports');
    await userEvent.click(screen.getByTestId('btn-post'));

    // 新規登録後はSportsが存在すること
    expect(await screen.findByText('Sports')).toBeInTheDocument();
  });

  // データを削除できること（id 1）
  it('5: Should delete segment(id 1) and also from list', async () => {
    render(
      <Provider store={store}>
        <Segment />
      </Provider>
    );
    // 読み込む前はSedanとSUVが存在しないこと
    expect(screen.queryByText('Sedan')).toBeNull();
    expect(screen.queryByText('SUV')).toBeNull();

    // 削除前はSedanとSUVが表示されていること
    expect(await screen.findByText('Sedan')).toBeInTheDocument();
    expect(await screen.findByText('SUV')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Sedan');
    expect(screen.getByTestId('list-2').textContent).toBe('SUV');

    // 1番目の削除ボタンをクリックすると削除メッセージが表示され、データが削除されること
    await userEvent.click(screen.getByTestId('delete-seg-1'));
    expect(await screen.findByText('Deleted in segment!')).toBeInTheDocument();
    expect(screen.queryByText('Sedan')).toBeNull();
  });

  // データを削除できること（id 2）
  it('6: Should delete segment(id 2) and also from list', async () => {
    render(
      <Provider store={store}>
        <Segment />
      </Provider>
    );
    // 読み込む前はSedanとSUVが存在しないこと
    expect(screen.queryByText('Sedan')).toBeNull();
    expect(screen.queryByText('SUV')).toBeNull();

    // 削除前はSedanとSUVが表示されていること
    expect(await screen.findByText('Sedan')).toBeInTheDocument();
    expect(await screen.findByText('SUV')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Sedan');
    expect(screen.getByTestId('list-2').textContent).toBe('SUV');

    // 2番目の削除ボタンをクリックすると削除メッセージが表示され、データが削除されること
    await userEvent.click(screen.getByTestId('delete-seg-2'));
    expect(await screen.findByText('Deleted in segment!')).toBeInTheDocument();
    expect(screen.queryByText('SUV')).toBeNull();
  });

  // 更新ボタンを押下するとデータが更新されること（id 1）
  it('7: Should delete segment(id 1) and also in the list', async () => {
    render(
      <Provider store={store}>
        <Segment />
      </Provider>
    );
    // 読み込む前はSedanとSUVが存在しないこと
    expect(screen.queryByText('Sedan')).toBeNull();
    expect(screen.queryByText('SUV')).toBeNull();

    // 読み込んだ後はSedanとSUVが表示されていること
    expect(await screen.findByText('Sedan')).toBeInTheDocument();
    expect(await screen.findByText('SUV')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Sedan');
    expect(screen.getByTestId('list-2').textContent).toBe('SUV');

    // 1番目のEditボタンを押下して更新ボタンを押下するとデータが更新されること
    const inputElement = screen.getByPlaceholderText('new segment name');
    await userEvent.click(screen.getByTestId('edit-seg-1'));
    await userEvent.clear(inputElement);
    await userEvent.type(inputElement, 'new Sedan');
    await userEvent.click(screen.getByTestId('btn-post'));
    expect(await screen.findByText('Updated in segment!')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('new Sedan');
  });

  // 更新ボタンを押下するとデータが更新されること（id 2）
  it('8: Should delete segment(id 2) and also in the list', async () => {
    render(
      <Provider store={store}>
        <Segment />
      </Provider>
    );
    // 読み込む前はSedanとSUVが存在しないこと
    expect(screen.queryByText('Sedan')).toBeNull();
    expect(screen.queryByText('SUV')).toBeNull();

    // 読み込んだ後はSedanとSUVが表示されていること
    expect(await screen.findByText('Sedan')).toBeInTheDocument();
    expect(await screen.findByText('SUV')).toBeInTheDocument();
    expect(screen.getByTestId('list-1').textContent).toBe('Sedan');
    expect(screen.getByTestId('list-2').textContent).toBe('SUV');

    // 2番目のEditボタンを押下して更新ボタンを押下するとデータが更新されること
    const inputElement = screen.getByPlaceholderText('new segment name');
    await userEvent.click(screen.getByTestId('edit-seg-2'));
    await userEvent.clear(inputElement);
    await userEvent.type(inputElement, 'new SUV');
    await userEvent.click(screen.getByTestId('btn-post'));
    expect(await screen.findByText('Updated in segment!')).toBeInTheDocument();
    expect(screen.getByTestId('list-2').textContent).toBe('new SUV');
  });
});
```