## フォルダ構成

```ts
.
├── components
│   └── SiteHeader.tsx
├── pages
│   └── index.tsx
```

## 全体

### pages/index.tsx

```tsx
import { SiteHeader } from '@/components/SiteHeader';
import Head from 'next/head';
import Link from 'next/link';

export default function Home() {
  return (
    <>
      <Head>
        <title key='pagetitle'>Welcome to CoolNomad Travel Blog</title>
        <meta
          name='description'
          content='coolnomad travel blog - read our travel stories'
          key='metadescription'
        />
      </Head>
      <div className="min-h-screen bg-[url('/home.jpg')] relative">
        <div className='absolute bg-slate-900 inset-0 z-0 opacity-40'></div>
        <SiteHeader className='z-10 relative' />
        <main>
          <div className='min-h-[50vh] flex flex-col items-center justify-center z-10 relative'>
            <h1 className='text-6xl text-center text-slate-100'>
              Welcome to <span className='text-yellow-400'>CoolNomad</span>{' '}
              Travel Blog
            </h1>
            <div className='mt-20'>
              <Link
                href='/blog'
                className='text-2xl text-slate-100 border-slate-100 border-2 rounded-md py-3 px-4 hover:bg-yellow-300 hover:text-slate-800 hover:border-yellow-300 transition'
              >
                Read Blog
              </Link>
            </div>
          </div>
        </main>
      </div>
    </>
  );
}
```

## Headコンポーネント

- headerタグと同じ
- title, metaタグの追加
- keyを使用して重複タグを避ける（同じキーの場合、レンダリング時に後者のみレンダリングされる）

### pages/index.tsx

```tsx
//...
      <Head>
        <title key='pagetitle'>Welcome to CoolNomad Travel Blog</title>
        <meta
          name='description'
          content='coolnomad travel blog - read our travel stories'
          key='metadescription'
        />
      </Head>
//...
```

## ヒーロー画像

### pages/index.tsx

- bg-[url(....)]でpublicフォルダの画像を指定
- 背景の上の要素を強調させるために暗い色を重ねる

```tsx
//...
      <div className="min-h-screen bg-[url('/home.jpg')] relative">
        <div className='absolute bg-slate-900 inset-0 z-0 opacity-40'></div>
//...
```

## SiteHeaderコンポーネント

- すべてのページで共通のナビゲーションバー

### components/SiteHeader.tsx

```tsx
import Image from 'next/image';
import Link from 'next/link';

export function SiteHeader({ className }: { className: string }) {
  return (
    <header
      className={`container mx-auto lg:max-w-4xl flex items-center justify-between ${className}`}
    >
      <div className='logo-area'>
        <Link href='/' className='flex justify-center'>
          <Image
            src='/CoolNomad.svg'
            alt='CoolNomad'
            width={180}
            height={120}
          />
        </Link>
      </div>
      <nav className='text-slate-100'>
        <ul
          className='
                flex justify-center [&>li>a]:px-3 [&>li>a]:py-2 [&>li>a:hover]:text-yellow-400\
                [&>li>a]:transition text-xl
              '
        >
          <li>
            <Link href='/'>Home</Link>
          </li>
          <li>
            <Link href='/blog'>Blog</Link>
          </li>
          <li>
            <Link href='/about'>About</Link>
          </li>
          <li>
            <Link href='/contact'>Contact</Link>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

## main要素

### pages/index.tsx 

- ブログのタイトル
- ブログを読むボタンを設置

```tsx
//...
        <main>
          <div className='min-h-[50vh] flex flex-col items-center justify-center z-10 relative'>
            <h1 className='text-6xl text-center text-slate-100'>
              Welcome to <span className='text-yellow-400'>CoolNomad</span>{' '}
              Travel Blog
            </h1>
            <div className='mt-20'>
              <Link
                href='/blog'
                className='text-2xl text-slate-100 border-slate-100 border-2 rounded-md py-3 px-4 hover:bg-yellow-300 hover:text-slate-800 hover:border-yellow-300 transition'
              >
                Read Blog
              </Link>
            </div>
          </div>
        </main>
//...
```