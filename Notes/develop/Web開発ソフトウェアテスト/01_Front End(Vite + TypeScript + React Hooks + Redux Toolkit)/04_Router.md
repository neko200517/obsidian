---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - vite
  - typescript
  - react-hooks
  - redux-toolkit
  - test
aliases:
  - <% tp.file.title %>
---

## Router

scr/App.js にルーティング機能を実装する

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
