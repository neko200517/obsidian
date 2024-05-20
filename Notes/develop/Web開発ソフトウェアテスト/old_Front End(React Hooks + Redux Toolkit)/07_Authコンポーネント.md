---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - javascript
  - react-hooks
  - redux-toolkit
  - test
  - old
aliases:
  - <% tp.file.title %>
---

## Auth コンポーネントの作成

ユーザー登録、ログイン画面を作成する

```js
// src/components/Auth.js

import React, { useState } from 'react';
import styles from './Auth.module.css';
import { FlipCameraAndroid } from '@material-ui/icons';
import { useNavigate } from 'react-router-dom';
import { useDispatch } from 'react-redux';
import { fetchAsyncLogin, fetchAsyncRegister } from '../features/authSlice';

const Auth = () => {
  const navigate = useNavigate();
  const dispatch = useDispatch();
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [isLogin, setIsLogin] = useState(true);
  const [successMsg, setSuccessMsg] = useState('');

  // ログイン処理
  const login = async () => {
    // dispatchでログインAPIを実行
    const result = await dispatch(
      fetchAsyncLogin({ username: username, password: password })
    );
    if (fetchAsyncLogin.fulfilled.match(result)) {
      // ログイン成功
      setSuccessMsg('Successfully logged in!');
      navigate('/vehicle');
    } else {
      // ログイン失敗
      setSuccessMsg('Login error!');
    }
  };

  // 登録処理
  const register = async () => {
    const result = await dispatch(
      fetchAsyncRegister({ username: username, password: password })
    );
    if (fetchAsyncRegister.fulfilled.match(result)) {
      // 登録成功
      login();
    } else {
      // 登録失敗
      setSuccessMsg('Registration error!');
    }
  };

  const authUser = async (e) => {
    e.preventDefault();
    if (isLogin) {
      login();
    } else {
      register();
    }
  };

  return (
    <div className={styles.auth__root}>
      <form onSubmit={authUser}>
        <span className={styles.auth__status}>{successMsg}</span>
        <div className={styles.auth__input}>
          <label data-testid='label-username'>username: </label>
          <input
            data-testid='input-username'
            type='text'
            value={username}
            onChange={(e) => setUsername(e.target.value)}
          />
        </div>
        <div className={styles.auth__input}>
          <label data-testid='label-password'>password: </label>
          <input
            data-testid='input-password'
            type='password'
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
        </div>
        <div>
          <button type='submit'>{isLogin ? 'Login' : 'Regist'}</button>
        </div>
        <div>
          <FlipCameraAndroid
            data-testid='toggle-icon'
            className={styles.auth__toggle}
            onClick={() => setIsLogin((x) => !x)}
          />
        </div>
      </form>
    </div>
  );
};

export default Auth;
```
