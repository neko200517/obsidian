## 複数のReducerを組み合わせる

### src/reducers/index.tsを作成

```sh
cd src/reducers
touch index.ts
```

### src/reducers/index.ts

```ts
import { combineReducers } from 'redux';
import { counterReducer } from './counter';
import { isLoginReducer } from './isLogin';

export const allReducers = combineReducers({
  counter: counterReducer,
  isLogin: isLoginReducer,
});
```

## src/main.tsx

```ts
import { createStore } from 'redux';
import { allReducers } from './reducers';
const store = createStore(allReducers);
```

## Chrome拡張のRedux dev toolsのためにcreateStoreを修正

```ts
const store = createStore(
  allReducers,
  (window as any).__REDUX_DEVTOOLS_EXTENSION__ &&
    (window as any).__REDUX_DEVTOOLS_EXTENSION__()
);
```

==現在のバージョンでは対応していない？==
