## src/page.tsx

Homeコンポーネントでデータを取得し、ArticleListコンポーネントに渡す。

```tsx
import ArticleList from './components/ArticleList';
import { getAllArticles } from '@/blogAPI';

export default async function Home() {
  const articles = await getAllArticles();
  return (
    <>
      <div className='md:flex'>
        <section className='w-full md:w-2/3 flex flex-col items-center px-3'>
          <ArticleList articles={articles} />
        </section>

        {* 中略 *}
      </div>
    </>
  );
}
```

## components/ArticleList.tsx 

受け取ったarticlesをmapでループし、ArticleCardコンポーネントに渡す。

```tsx
import React from 'react';
import { Article } from '@/types';
import { ArticleCard } from './ArticleCard';

type ArticleListProps = {
  articles: Article[];
};

const ArticleList = async ({ articles }: ArticleListProps) => {
  return (
    <div>
      {articles.map((article) => (
        <ArticleCard key={article.id} article={article} />
      ))}
    </div>
  );
};

export default ArticleList;
```

## components/ArticleCard.tsx

記事のサムネイル、カテゴリ、投稿内容などを表示するカードを作成する。

### 日付のフォーマット

`new Date(article.createdAt).toLocalString()` を呼び出すことでフォーマットされた日時を取得できる。

```tsx
import Image from 'next/image';
import Link from 'next/link';
import React from 'react';
import { Article } from '@/types';

type ArticleCardProps = {
  article: Article;
};

export const ArticleCard = ({ article }: ArticleCardProps) => {
  return (
    <article className='shadow my-4 flex flex-col'>
      <Link href={`articles/${article.id}`} className='hover:opacity-75'>
        <Image src={article.image_url} alt='' width={1280} height={300} />
      </Link>
      <div className='bg-white flex flex-col justify-start p-6'>
        <Link
          href={`articles/${article.id}`}
          className='text-blue-700 pb-4 font-bold'
        >
          {article.category}
        </Link>
        <Link
          href={`articles/${article.id}`}
          className='text-slate-900 text-3xl font-bold hover:text-gray-700 pb-4'
        >
          {article.title}
        </Link>
        <p className='text-sm pb-3 text-slate-900'>
          By {article.publisher}, Published on
          {new Date(article.createdAt).toLocalString()}
        </p>
        <Link href={`articles/${article.id}`} className='text-slate-900 pb-6'>
          {article.content}
        </Link>
        <Link
          href={`articles/${article.id}`}
          className='text-pink-800 hover:text-black'
        >
          続きを読む
        </Link>
      </div>
    </article>
  );
};
```
