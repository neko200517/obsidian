## src/actions/index.ts

先にactionsディレクトリにactionを作成する。

```ts
export const increment = () => {
  return {
    type: 'INCREMENT',
  };
};

export const decrement = () => {
  return {
    type: 'DECREMENT',
  };
};

export const login = () => {
  return {
    type: 'LOGIN',
  };
};

export const logout = () => {
  return {
    type: 'LOGOUT',
  };
};
```

## App.tsx

useDispatchを使ってactionを呼び出す。

```ts
// 中略
import { useSelector, useDispatch } from 'react-redux';
import { CombineStateType } from './reducers';
import { increment, decrement, login } from './actions';

function App() {
  const counter = useSelector((state: CombineStateType) => state.counter);
  const isLogin = useSelector((state: CombineStateType) => state.isLogin);
  const dispatch = useDispatch();

  return (
    <>
      <h2>Hello</h2>
      <h3>カウント：{counter}</h3>
      <h3>ログイン：{isLogin ? 'true' : 'false'}</h3>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(login())}>
        {isLogin ? 'Logout' : 'Login'}
      </button>
    </>
  );
}

export default App;

```