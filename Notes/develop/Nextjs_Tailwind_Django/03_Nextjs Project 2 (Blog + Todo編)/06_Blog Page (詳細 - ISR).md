## ISR

基本的にはプリレンダリングされるが、情報の更新とユーザーのアクセスをトリガーとして新しいページを再生成する仕組みが ISR となる。

例）
サーバサイド：データ更新
クライアント：1回目のアクセス
クライアント：古いページを表示
サーバサイド：3秒後にページ再生成
クライアント：ブラウザ再読込
クライアント：新しいページを表示

```ts
{ next: { revalidate: 3 } }
```

## src/app/lib/posts.ts

- すべての Post の ID を取得する関数の追加
- id を引数に与えると Post データを取得する関数の追加

```ts
import { PostType } from '@/app/types';

// 全てのデータを取得する(日付を降順にソート)
export async function getAllPostData(): Promise<PostType[]> {
  const res = await fetch(
    new URL(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/list-post/`),
    { next: { revalidate: 3 } }
  );

  const data = (await res.json()) as PostType[];

  const sortedData = [...data].sort(
    (a, b) =>
      new Date(b.created_at).getTime() - new Date(a.created_at).getTime()
  );

  return sortedData;
}

// 全てのデータのIDを取得する
export async function getAllPostIds(): Promise<{ id: string }[]> {
  const res = await fetch(
    new URL(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/list-post/`)
  );
  const data = await res.json();

  return data.map((post: PostType) => ({
    id: String(post.id),
  }));
}

// 特定のデータを取得する
export async function getPostDataById(id: string): Promise<PostType> {
  const res = await fetch(
    new URL(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/detail-post/${id}/`),
    { next: { revalidate: 3 } }
  );

  return await res.json();
}
```

## src/app/blog/[id]/page.tsx 

- Post 詳細ページ 
- generateStaticParams を上書きして SSG でサイトを生成する

```tsx
import { Metadata } from 'next';
import { getAllPostIds, getPostDataById } from '@/app/lib/post';
import { ReturnLink } from '@/app/components/ReturnLink';
import { Suspense } from 'react';

export const metadata: Metadata = {
  title: 'Blog Detail Page',
};

export async function generateStaticParams() {
  return getAllPostIds();
}

export default async function BlogDetailPage({
  params,
}: {
  params: { id: string };
}) {
  const post = await getPostDataById(params.id);

  return (
    <>
      <Suspense fallback={<>Loading...</>}>
        <p className='m-4'>
          {'ID : '}
          {post.id}
        </p>
        <p className='mb-4 text-xl font-bold'>{post.title}</p>
        <p className='mb-12'>{post.created_at}</p>
        <p className='px-10'>{post.content}</p>
      </Suspense>
      <ReturnLink href='/blog'>Back to blog page</ReturnLink>
    </>
  );
}
```

## src/app/components/Post.tsx 

- Post 詳細ページのリンクを追加

```tsx
import React from 'react';
import { PostType } from '../types';
import Link from 'next/link';

export function Post({ post }: { post: PostType }) {
  return (
    <div>
      <span>{post.id}</span>
      {' : '}
      <Link href={`/blog/${post.id}`}>
        <span className='cursor-pointer text-white border-b border-gray-500 hover:bg-gray-600'>
          {post.title}
        </span>
      </Link>
    </div>
  );
}
```

## src/app/components/ReturnLink.tsx 

- 戻るボタンをコンポーネント化

```tsx
import Link from 'next/link';

export function ReturnLink({
  href,
  children,
}: {
  href: string;
  children: React.ReactNode;
}) {
  return (
    <>
      <Link href={href}>
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
          <span>{children}</span>
        </div>
      </Link>
    </>
  );
}
```