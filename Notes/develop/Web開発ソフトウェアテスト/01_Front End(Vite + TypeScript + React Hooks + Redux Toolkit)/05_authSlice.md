## authSliceの作成 

src/features/authSliceファイルを作成して、ログインしたユーザー情報を保存する

```bash
mkdir features 
touch features/authSlice.ts
```

```js
// src/features/authSlice.ts

import { createAsyncThunk, createSlice, PayloadAction } from '@reduxjs/toolkit';
import axios, { AxiosResponse } from 'axios';

interface Auth {
  username: string;
  password: string;
}

interface Profile {
  id: number;
  username: string;
}

interface AuthState {
  profile: Profile;
}

const initialState: AuthState = {
  profile: {
    id: 0,
    username: '',
  },
};

const apiUrl = 'http://localhost:8000/';

// ログインAPIの実行と値の取得
export const fetchAsyncLogin = createAsyncThunk(
  'login/post',
  async (auth: Auth) => {
    const res: AxiosResponse = await axios.post(`${apiUrl}api/auth/`, auth, {
      headers: {
        'Content-Type': 'application/json',
      },
    });
    return res.data;
  }
);

// ユーザー作成APIの実行と値の取得
export const fetchAsyncRegister = createAsyncThunk(
  'register/post',
  async (auth: Auth) => {
    const res: AxiosResponse = await axios.post(`${apiUrl}api/create/`, auth, {
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
    const res: AxiosResponse = await axios.get(`${apiUrl}api/profile/`, {
      headers: {
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

export const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {},
  extraReducers: (builder) => {
    // ログインが成功したらローカルストレージにトークンを保存
    builder.addCase(
      fetchAsyncLogin.fulfilled,
      (_, action: PayloadAction<{ token: string }>) => {
        localStorage.setItem('token', action.payload.token);
      }
    );
    // ユーザー情報の取得が成功したらidとusernameを保存
    builder.addCase(
      fetchAsyncGetProfile.fulfilled,
      (state, action: PayloadAction<Profile>) => {
        return {
          ...state,
          profile: action.payload,
        };
      }
    );
  },
});

export const selectProfile = (state: { auth: AuthState }) => state.auth.profile;

export default authSlice.reducer;
```

## Reducerの登録

作成したReducerを登録する

```bash
mkdir src/app
touch src/store.ts
touch src/hooks.ts
```

```ts
// src/hooks.ts 

import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';
import type { RootState, AppDispatch } from './store';

// Use throughout your app instead of plain `useDispatch` and `useSelector`
export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

```ts
// src/store.ts 

import { configureStore, ThunkAction, Action } from '@reduxjs/toolkit';
import authReducer from '../features/authSlice';
import vehicleReducer from '../features/vehicleSlice';

export const store = configureStore({
  reducer: {
    auth: authReducer,
    vehicle: vehicleReducer,
  },
});

export type AppDispatch = typeof store.dispatch;
export type RootState = ReturnType<typeof store.getState>;
export type AppThunk<ReturnType = void> = ThunkAction<
  ReturnType,
  RootState,
  unknown,
  Action<string>
>;
```
