## 新規投稿ページを作成する

### ポイント
- onSubmit, useState を使用するので 'use client' を記述する。
- useRouterでリダイレクトと更新を行っている。
- 実務では React Hook Form を利用するのを推奨。

```tsx
// src/app/new/page.tsx

'use client';

import React, { useState } from 'react';
import { useRouter } from 'next/navigation';

import { createArticle } from '@/blogAPI';

const CreateBlogPage = () => {
  const router = useRouter();
  const [id, setId] = useState<string>('');
  const [title, setTitle] = useState<string>('');
  const [content, setContent] = useState<string>('');

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    await createArticle({
      id: id,
      title: title,
      content: content,
      publisher: '匿名希望',
      category: 'Other',
      image_url: `https://source.unsplash.com/collection/1346951/1000x500?sig=${id}`,
    });

    router.push('/'); // トップページにリダイレクト
    router.refresh(); // 更新
  };

  return (
    <div className='py-8 px-4 md:px-12'>
      <h2 className='text-2xl font-bold mb-4'>ブログ新規作成</h2>
      <form
        className='bg-slate-200 p-6 rounded shadow-lg'
        onSubmit={handleSubmit}
      >
        <div className='mb-4'>
          <label className='text-gray-700 text-sm font-bold mb-2'>URL</label>
          <input
            type='text'
            className='shadow border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none'
            value={id}
            onChange={(e) => setId(e.target.value)}
            required
          />
        </div>

        <div className='mb-4'>
          <label className='text-gray-700 text-sm font-bold mb-2'>
            タイトル
          </label>
          <input
            type='text'
            className='shadow border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none'
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            required
          />
        </div>

        <div className='mb-4'>
          <label className='text-gray-700 text-sm font-bold mb-2'>本文</label>
          <textarea
            className='shadow border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none'
            value={content}
            onChange={(e) => setContent(e.target.value)}
            required
          />
        </div>

        <button
          type='submit'
          className='py-2 px-4 border rounded-md bg-orange-300'
        >
          投稿
        </button>
      </form>
    </div>
  );
};

export default CreateBlogPage;
```