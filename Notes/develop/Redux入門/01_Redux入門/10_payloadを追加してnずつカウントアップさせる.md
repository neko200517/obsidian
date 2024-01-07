## src/actions/index.ts

新たにpalyloadを追加する。

```ts
export const increment = (number: number) => {
  return {
    type: 'INCREMENT',
    payload: number,
  };
};

export const decrement = (number: number) => {
  return {
    type: 'DECREMENT',
    payload: number,
  };
};
//中略
```

## src/reducres/counter.ts 

加算/減算する値をaction.payloadにする。

```ts
export type CounterAction = {
  type: 'INCREMENT' | 'DECREMENT';
  payload: number;
};

export const counterReducer = (state = 0, action: CounterAction) => {
  switch (action.type) {
    case 'INCREMENT':
      return state + action.payload;
    case 'DECREMENT':
      return state - action.payload;
    default:
      return state;
  }
};
```

## src/App.tsx

引数を与えることでpayloadに値を与えることができる。

```ts
// 中略
      <button onClick={() => dispatch(increment(7))}>+</button>
      <button onClick={() => dispatch(decrement(7))}>-</button>
// 中略
```
