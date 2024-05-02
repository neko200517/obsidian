## 必要なライブラリをインストール

```bash
npm i universal-cookie
npm i swr
```

## .env.local の作成

```bash
touch .env.local
```

```txt
NEXT_PUBLIC_RESTAPI_URL=https://nextjs-basics-02-api.onrender.com
```

## src/app/components/Auth.tsx

- サインイン・登録機能の実装 
- API から取得した JWT トークンを cookie に保存
- サインイン後、MainPage に遷移する
- hooks を使うので use client で処理

```tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';
import Cookie from 'universal-cookie';

const cookie = new Cookie();

export default function Auth() {
  const router = useRouter();
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [isLogin, setIsLogin] = useState(true);

  const login = async () => {
    try {
      await fetch(
        `${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/auth/jwt/create/`,
        {
          method: 'POST',
          body: JSON.stringify({ username: username, password: password }),
          headers: {
            'Content-Type': 'application/json',
          },
        }
      )
        .then((res) => {
          if (res.status === 400) {
            throw 'authentication failed';
          } else if (res.ok) {
            return res.json();
          }
        })
        .then((data) => {
          const options = { path: '/' };
          cookie.set('access_token', data.access, options);
        });
      router.push('/main');
    } catch (e: any) {
      alert(e.message);
    }
  };

  const authUser = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (isLogin) {
      login();
    } else {
      try {
        await fetch(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/register/`, {
          method: 'POST',
          body: JSON.stringify({ username: username, password: password }),
          headers: {
            'Content-Type': 'application/json',
          },
        }).then((res) => {
          if (res.status === 400) {
            throw 'authentication failed';
          }
        });
        login();
      } catch (e: any) {
        alert(e.message);
      }
    }
  };

  return (
    <div className='max-w-md w-full space-y-8'>
      <div>
        <img
          className='mx-auto h-12 w-auto  min-w-80'
          src='https://tailwindui.com/img/logos/workflow-mark-indigo-600.svg'
          alt='Workflow'
        />
        <h2 className='mt-6 text-center text-3xl font-extrabold text-white'>
          {isLogin ? 'Login' : 'Sign up'}
        </h2>
      </div>
      <form className='mt-8 space-y-6' onSubmit={authUser}>
        <input type='hidden' name='remember' value='true' />
        <div className='rounded-md shadow-sm -space-y-px'>
          <div>
            <input
              name='username'
              type='text'
              autoComplete='username'
              required
              className='appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm'
              placeholder='Username'
              value={username}
              onChange={(e) => {
                setUsername(e.target.value);
              }}
            />
          </div>
          <div>
            <input
              name='password'
              type='password'
              autoComplete='current-password'
              required
              className='appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm'
              placeholder='Password'
              value={password}
              onChange={(e) => {
                setPassword(e.target.value);
              }}
            />
          </div>
        </div>

        <div className='flex items-center justify-center'>
          <div className='text-sm'>
            <span
              onClick={() => setIsLogin(!isLogin)}
              className='cursor-pointer font-medium text-white hover:text-indigo-500'
            >
              change mode ?
            </span>
          </div>
        </div>

        <div>
          <button
            type='submit'
            className='group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500'
          >
            {isLogin ? 'Login with JWT' : 'Create new user'}
          </button>
        </div>
      </form>
    </div>
  );
}
```

## src/app/main/page.tsx

- 簡易的なページを作成
- サインオフ機能の実装（cookie削除）
- hooks を使うので use client で処理

```tsx
'use client';

import Cookie from 'universal-cookie';
import { useRouter } from 'next/navigation';

const cookie = new Cookie();

export default function MainPage() {
  const router = useRouter();
  const logout = () => {
    cookie.remove('access_token', { path: '/' });
    router.push('/');
  };

  return (
    <div>
      <a href='/'>
        <svg
          onClick={logout}
          className='mt-10 cursor-pointer w-6 h-6'
          xmlns='http://www.w3.org/2000/svg'
          fill='none'
          viewBox='0 0 24 24'
          stroke-width='1.5'
          stroke='currentColor'
        >
          <path
            stroke-linecap='round'
            stroke-linejoin='round'
            d='M8.25 9V5.25A2.25 2.25 0 0 1 10.5 3h6a2.25 2.25 0 0 1 2.25 2.25v13.5A2.25 2.25 0 0 1 16.5 21h-6a2.25 2.25 0 0 1-2.25-2.25V15m-3 0-3-3m0 0 3-3m-3 3H15'
          />
        </svg>
      </a>
    </div>
  );
}
```