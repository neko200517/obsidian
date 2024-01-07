## src/reducers.ts

```ts
import { CombinedState, combineReducers } from 'redux';
import { counterReducer } from './counter';
import { isLoginReducer } from './isLogin';

export const allReducers = combineReducers({
  counter: counterReducer,
  isLogin: isLoginReducer,
});

export type CombineStateType = CombinedState<{
  counter: number;
  isLogin: boolean;
}>;

```

## App.tsx

useSelectorを使って状態にアクセスする。

```ts
import './App.css';
import { useSelector } from 'react-redux';
import { CombineStateType } from './reducers';

function App() {
  const counter = useSelector((state: CombineStateType) => state.counter);
  const isLogin = useSelector((state: CombineStateType) => state.isLogin);

  return (
    <>
      <h2>Hello</h2>
      <h3>カウント：{counter}</h3>
      <h3>ログイン：{isLogin ? 'true' : 'false'}</h3>
    </>
  );
}

export default App;

```
