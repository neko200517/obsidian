## 概要

Next.jsはあらかじめ静的ページを生成することでapiからデータを取得しなくてもよくなるため更新頻度が低いページでパフォーマンスを発揮する。

## src/app/lib/posts.ts

・getAllPostIds：後でStaticなIDを生成するために追加
・getPostDataById：API から id で指定して取得

```ts
import { notFound } from 'next/navigation';
import { PostType } from '../types';

const apiUrl = 'https://jsonplaceholder.typicode.com/posts';

export const getAllPostsData = async (): Promise<PostType[]> => {
  const res = await fetch(new URL(apiUrl));

  if (!res.ok) {
    throw new Error('Error!');
  }

  return await res.json();
};

export const getAllPostIds = async () => {
  const res = await fetch(new URL(apiUrl));
  const posts = await res.json();

  return posts.map((post: any) => ({
    id: String(post.id),
  }));
};

export const getAllPostsDataById = async (id: string): Promise<PostType> => {
  const res = await fetch(new URL(`${apiUrl}/${id}`));

  if (res.status === 404) {
    notFound();
  }

  if (!res.ok) {
    throw new Error('Error!');
  }

  return await res.json();
};
```

## src/app/blog/[id]/page.tsx

・generateStaticParams：静的ページ生成のための id リストを取得
・params で blog/[id] の形式でパスパラメータを取得する Next.js の記法
　・generateStaticParams から返ってくる値とパスパラメータは互換性のある形式にすること
　・例）{params: { id: string }} → [ {id: 1}, {id: 2} ]

```tsx
import { getAllPostIds, getPsotDataById } from '@/app/lib/posts';
import Image from 'next/image';
import Link from 'next/link';
import { Suspense } from 'react';

export const generateStaticParams = async () => {
  return getAllPostIds();
};

const BlogDetail = async ({ params }: { params: { id: string } }) => {
  const post = await getPsotDataById(params.id);

  return (
    <Suspense fallback={<div>Loading...</div>}>
      <p className='m-4'>
        {'ID : '}
        {post.id}
      </p>
      <p className='mb-8 text-xl font-bold'>{post.title}</p>
      <p className='px-10'>{post.body}</p>
      <Link href='/blog'>
        <div className='flex cursor-pointer mt-12 items-center'>
          <Image
            className='mr-3'
            src='/chevron-double-left.svg'
            width={24}
            height={24}
            alt='chevron-double-left'
          />
          <span>Back to blog page</span>
        </div>
      </Link>
    </Suspense>
  );
};

export default BlogDetail;
```

## src/next.config.mjs

output: "export" を指定すると /out ディレクトリに静的ページとして出力する

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
};

export default nextConfig;
```
