## Nextjs 13 以前の違い

Next 13 以前では SSG には getStaticProps を使用していたが、Nextjs 13 以上では特に指定しなくてもビルド時に静的ページを生成するようになった（ただしキャッシュしていない場合に限る）

## src/app/lib/posts.ts

- Post 一覧を日付の降順で取得する関数を追加
- fetch 関数にオプションを付けずにキャッシュする

```ts
import { PostType } from '@/app/types';

export async function getAllPostData(): Promise<PostType[]> {
  // 日付を降順ソート
  const descDateTime = (a: Date, b: Date) => {
    return a < b ? 1 : -1;
  };

  const res = await fetch(
    new URL(`${process.env.NEXT_PUBLIC_RESTAPI_URL}/api/list-post/`)
  );
  const posts = await res.json();
  const fileterdPosts = posts.sort((a: PostType, b: PostType) =>
    descDateTime(new Date(a.created_at), new Date(b.created_at))
  );
  return fileterdPosts;
}
```

## src/app/blog/page.tsx

- Post 一覧を表示する

```tsx
import { Metadata } from 'next';
import Link from 'next/link';
import { getAllPostData } from '../lib/post';
import { Post } from '../components/Post';

export const metadata: Metadata = {
  title: 'Blog Page',
};

export default async function BlogPage() {
  const fileterdPosts = await getAllPostData();
  return (
    <>
      <ul>
        {fileterdPosts &&
          fileterdPosts.map((post) => <Post key={post.id} post={post} />)}
      </ul>
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

## src/app/components/Post.tsx 

```tsx
import React from 'react';
import { PostType } from '../types';

export function Post({ post }: { post: PostType }) {
  return (
    <div>
      <span>{post.id}</span>
      {' : '}
      <span className='cursor-pointer text-white border-b border-gray-500 hover:bg-gray-600'>
        {post.title}
      </span>
    </div>
  );
}
```

## ビルド 

```bash
npm run build
```

ビルドした後、 .next/server/app 配下に 静的な html が生成されることを確認する