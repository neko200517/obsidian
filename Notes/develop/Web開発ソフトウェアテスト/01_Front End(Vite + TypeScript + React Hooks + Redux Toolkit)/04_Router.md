## Router

scr/App.jsにルーティング機能を実装する

```ts
import styles from './App.module.css';
import { Route, BrowserRouter, Routes } from 'react-router-dom';
import Auth from './components/Auth';
import MainPage from './components/MainPage';

const App = () => {
  return (
    <div className={styles.app__root}>
      <BrowserRouter>
        <Routes>
          <Route path='/' element={<Auth />} />
          <Route path='/vehicle' element={<MainPage />} />
        </Routes>
      </BrowserRouter>
    </div>
  );
};

export default App;
```