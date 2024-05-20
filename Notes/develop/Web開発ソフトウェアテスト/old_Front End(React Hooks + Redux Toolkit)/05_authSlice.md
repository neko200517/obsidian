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

## authSlice の作成

src/features/authSlice ファイルを作成して、ログインしたユーザー情報を保存する

```bash
mkdir features
touch features/authSlice.js
```

```js
import { createAsyncThunk, createSlice } from '@reduxjs/toolkit';
import axios from 'axios';

const apiUrl = 'http://localhost:8000/';

// ログインAPIの実行と値の取得
export const fetchAsyncLogin = createAsyncThunk('login/post', async (auth) => {
  const res = await axios.post(`${apiUrl}api/auth/`, auth, {
    headers: {
      'Content-Type': 'application/json',
    },
  });
  return res.data;
});

// ユーザー作成APIの実行と値の取得
export const fetchAsyncRegister = createAsyncThunk(
  'register/post',
  async (auth) => {
    const res = await axios.post(`${apiUrl}api/create/`, auth, {
      headers: {
        'Content-Type': 'application/json',
      },
    });
    return res.data;
  }
);

// ユーザー情報APIの実行と値の取得
export const fetchAsyncGetProfile = createAsyncThunk(
  'profile/get',
  async () => {
    const res = await axios.get(`${apiUrl}api/profile/`, {
      headers: {
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

export const authSlice = createSlice({
  name: 'auth',
  initialState: {
    profile: {
      id: 0,
      username: '',
    },
  },
  reducers: {},
  extraReducers: (builder) => {
    // ログインが成功したらローカルストレージにトークンを保存
    builder.addCase(fetchAsyncLogin.fulfilled, (state, action) => {
      localStorage.setItem('token', action.payload.token);
    });
    // ユーザー情報の取得が成功したらidとusernameを保存
    builder.addCase(fetchAsyncGetProfile.fulfilled, (state, action) => {
      return {
        ...state,
        profile: action.payload,
      };
    });
  },
});

export const selectProfile = (state) => state.auth.profile;

export default authSlice.reducer;
```

## Reducer の登録

作成した Reducer を登録する

```bash
mkdir src/app
touch src/touch.js
```

```js
import { configureStore } from '@reduxjs/toolkit';
import authReducer from '../features/authSlice';

export const store = configureStore({
  reducer: {
    auth: authReducer,
  },
});
```
