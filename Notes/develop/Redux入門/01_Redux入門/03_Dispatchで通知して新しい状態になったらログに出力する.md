## 新しい状態を監視

```ts
// 状態を監視してconsole.logに出力
store.subscribe(() => console.log(store.getState()));
```

## 全体

```ts
import { createStore } from 'redux';

// 中略

type ActionType = { type: 'INCREMENT' | 'DECREMENT' };

// Actions
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

// Reducer
const counterReducer = (state = 0, action: ActionType) => {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
  }
};

// Store
let store = createStore(counterReducer);

// 状態を監視してconsole.logに出力
store.subscribe(() => console.log(store.getState()));

// Dispatch
store.dispatch(increment());
```