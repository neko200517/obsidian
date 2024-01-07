## src/main.tsx

Providerを挟むことでどこでもstoreにアクセスできるようになる。

```ts
// 中略
import { createStore } from 'redux';
import { allReducers } from './reducers/index.ts';
import { Provider } from 'react-redux';

const store = createStore(
  allReducers,
  (window as any).__REDUX_DEVTOOLS_EXTENSION__ &&
    (window as any).__REDUX_DEVTOOLS_EXTENSION__()
);

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
```
