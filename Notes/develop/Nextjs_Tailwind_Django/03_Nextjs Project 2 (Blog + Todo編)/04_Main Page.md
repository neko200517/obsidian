## src/app/main/page.tsx

- メインページ
- ブログページ、タスクページ、トップページへ戻るボタンを追加

```tsx
'use client';

import Cookie from 'universal-cookie';
import { useRouter } from 'next/navigation';
import Link from 'next/link';

const cookie = new Cookie();

export default function MainPage() {
  const router = useRouter();
  const logout = () => {
    cookie.remove('access_token', { path: '/' });
    router.push('/');
  };

  return (
    <>
      <div className='mb-10'>
        <Link href='/blog'>
          <span className='bg-indigo-500 mr-8 hover:bg-indigo-600 text-white px-4 py-12 rounded'>
            Visit Blog by SSG + ISR
          </span>
        </Link>
        <Link href='/task'>
          <span className='bg-gray-500 mr-8 hover:bg-gray-600 text-white px-4 py-12 rounded'>
            Visit Task by ISR + CSR
          </span>
        </Link>
      </div>

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
    </>
  );
}
```

## src/app/blog/page.tsx 

- ブログ一覧を表示するページ

```tsx
import { Metadata } from 'next';
import Link from 'next/link';

export const metadata: Metadata = {
  title: 'Blog Page',
};

export default function BlogPage() {
  return (
    <>
      <Link href='/main'>
        <div className='flex cursor-pointer mt-12'>
          <svg
            xmlns='http://www.w3.org/2000/svg'
            fill='none'
            viewBox='0 0 24 24'
            strokeWidth={1.5}
            stroke='currentColor'
            className='w-6 h-6 mr-3'
          >
            <path
              strokeLinecap='round'
              strokeLinejoin='round'
              d='m18.75 4.5-7.5 7.5 7.5 7.5m-6-15L5.25 12l7.5 7.5'
            />
          </svg>
          <span>Back to main page</span>
        </div>
      </Link>
    </>
  );
}
```

## src/app/task/page.tsx 

- タスク一覧を表示するページ

```tsx
import { Metadata } from 'next';
import Link from 'next/link';

export const metadata: Metadata = {
  title: 'Task Page',
};

export default function TaskPage() {
  return (
    <>
      <Link href='/main'>
        <div className='flex cursor-pointer mt-12'>
          <svg
            xmlns='http://www.w3.org/2000/svg'
            fill='none'
            viewBox='0 0 24 24'
            strokeWidth={1.5}
            stroke='currentColor'
            className='w-6 h-6 mr-3'
          >
            <path
              strokeLinecap='round'
              strokeLinejoin='round'
              d='m18.75 4.5-7.5 7.5 7.5 7.5m-6-15L5.25 12l7.5 7.5'
            />
          </svg>
          <span>Back to main page</span>
        </div>
      </Link>
    </>
  );
}
```