## Auth.tsxの実装

ログイン、サインアップ、UIの作成。

```tsx
// components/Auth.tsx

import { useState, FormEvent } from 'react';
import { CheckBadgeIcon, ArrowPathIcon } from '@heroicons/react/24/solid';
import { useMutateAuth } from '../hooks/useMutateAuth';

export const Auth = () => {
  const [email, setEmail] = useState('');
  const [pw, setPw] = useState('');
  const [isLogin, setIsLogin] = useState(true); // ログインモード... true: ログイン, false: サインイン
  const { loginMutation, registerMutation } = useMutateAuth();

  const submitAuthHandler = async (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (isLogin) {
      // ログイン
      loginMutation.mutate({
        email: email,
        password: pw,
      });
    } else {
      // サインアップ
      await registerMutation
        .mutateAsync({
          email: email,
          password: pw,
        })
        .then(() => {
          // 登録に成功したらログイン処理
          loginMutation.mutate({
            email: email,
            password: pw,
          });
        });
    }
  };

  return (
    <div className='flex justify-center items-center flex-col min-h-screen text-gray-600 font-mono'>
      {/* タイトル */}
      <div className='flex items-center'>
        <CheckBadgeIcon className='h-8 w-8 mr-2 text-blue-500' />
        <span className='text-center text3xl font-extrabold'>
          Todo app by React/Go(Echo)
        </span>
      </div>
      <h2 className='my-6'>{isLogin ? 'Login' : 'Create a new account'}</h2>

      {/* フォーム */}
      <form onSubmit={submitAuthHandler}>
        {/* Email */}
        <div>
          <input
            className='mb-3 px-3 text-sm py-2 border border-e-gray-300'
            name='email'
            type='email'
            autoFocus
            placeholder='Email address'
            onChange={(e) => setEmail(e.target.value)}
            value={email}
          />
        </div>
        {/* Password */}
        <div>
          <input
            className='mb-3 px-3 text-sm py-2 border border-e-gray-300'
            name='password'
            type='password'
            autoFocus
            placeholder='Password'
            onChange={(e) => setPw(e.target.value)}
            value={pw}
          />
        </div>
        {/* Button */}
        <div className='flex justify-center my-2'>
          <button
            className='disabled:opacity-40 py-2 px-4 rounded text-white bg-indigo-600'
            disabled={!email || !pw}
            type='submit'
          >
            {isLogin ? 'Login' : 'Signin'}
          </button>
        </div>
      </form>
      {/* ToggleButton */}
      <ArrowPathIcon
        className='h-6 w-6 my-2 text-blue-500 cursor-pointer'
        onClick={() => setIsLogin(!isLogin)}
      />
    </div>
  );
};
```