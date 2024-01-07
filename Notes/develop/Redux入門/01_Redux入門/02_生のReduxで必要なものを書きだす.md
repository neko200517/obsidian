## 概要

Dispatchを通知してStoreの状態を変更する。

![[Pasted image 20231027152831.png]]

## App.tsx

以下のStore, Reducer, Actionを作成する。

## createStoreをインポート

```ts
// 中略
import { createStore } from 'redux';
// 中略
```

## Storeを作成する

```ts
let store = createStore(counterReducer);
```

## Reducerを作成する

```ts
type ActionType = { type: 'INCREMENT' | 'DECREMENT' };

const counterReducer = (
  state = 0,
  action: ActionType
) => {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
  }
};
```

## Actionsを作成する

```ts
const increment = (): ActionType => {
  return {
    type: 'INCREMENT',
  };
};

const decrement = (): ActionType => {
  return {
    type: 'DECREMENT',
  };
};
```
