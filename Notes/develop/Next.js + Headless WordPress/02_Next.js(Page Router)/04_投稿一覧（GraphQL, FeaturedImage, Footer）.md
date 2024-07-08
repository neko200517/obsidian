## フォルダ構成

```tsx
.
├── components
│   ├── FeaturedImage.tsx
│   ├── SiteFooter.tsx
│   └── SiteHeader.tsx
├── lib
│   ├── graphqlRequest.ts
│   └── posts.ts
├── next.config.js
├── pages
│   ├── blog
│   │   └── index.tsx
├── types
│   └── posts.ts
```

## GraphQLクライアントの作成

### lib/graphqlRequest.ts

```ts
export default async function graphqlRequest(query: { query: string }) {
  const url = 'http://localhost:8000/graphql';
  const headers = { 'Content-Type': 'application/json' };

  const res = await fetch(url, {
    headers,
    method: 'POST',
    body: JSON.stringify(query),
  });

  const reJson = await res.json();
  return reJson;
}
```

## 投稿一覧取得関数の作成 

### lib/posts.ts

```ts
import { PostNode } from '@/types/post';
import graphqlRequest from './graphqlRequest';

export async function getAllPosts(): Promise<AllPost> {
  const query = {
    query: `query getAllPosts {
      posts {
        nodes {
          date
          slug
          title
          excerpt(format: RENDERED)
          featuredImage {
            node {
              mediaDetails {
                file
                sizes {
                  sourceUrl
                  width
                  height
                }
              }
            }
          }
          categories {
            nodes {
              name
              slug
            }
          }
        }
        pageInfo {
          startCursor
          endCursor
          hasNextPage
          hasPreviousPage      
        }
      }
    }
    `,
  };

  const resJson = await graphqlRequest(query);
  const allPosts = resJson.data.posts;

  return allPosts;
}
```

## 型情報の定義

### types/post.ts

```ts
export type PostNode = {
  date: string;
  slug: string;
  title: string;
  excerpt: string;
  featuredImage: {
    node: {
      mediaDetails: {
        file: string;
        sizes: {
          sourceUrl: string;
          width: string;
          height: string;
        }[];
      };
    };
  };
  categories: {
    nodes: {
      name: string;
      slug: string;
    }[];
  };
};

export type AllPost = {
  nodes: PostNode[];
  pageInfo: {
    startCursor: string;
    endCursor: string;
    hasNextPage: boolean;
    hasPreviousPage: boolean;
  };
};
```

## ポスト一覧全体

- getStaticPropsでサーバサイドで投稿一覧の取得 
	- クライアント側でallPostsを受け取る
- header, main, sectionタグを適切に使用してSEOを向上させる

### pages/blog/index.ts

```tsx
import Head from 'next/head';
import Link from 'next/link';
import { SiteHeader } from '@/components/SiteHeader';
import { getAllPosts } from '@/lib/posts';
import { AllPost } from '@/types/post';
import FeaturedImage from '@/components/FeaturedImage';
import SiteFooter from '@/components/SiteFooter';
import Date from '@/components/Date';

export async function getStaticProps() {
  const allPosts = await getAllPosts();

  return {
    props: {
      allPosts: allPosts,
    },
  };
}

export default function BlogHome({ allPosts }: { allPosts: AllPost }) {
  return (
    <>
      <Head>
        <title>Blog</title>
      </Head>
      <div className="h-[50vh] min-h-[20rem] bg-[url('/home.jpg')] relative">
        <div className='absolute bg-slate-900 inset-0 z-0 opacity-40' />

        <div className='container lg:max-w-4xl mx-auto'>
          <SiteHeader className='header-blog-home z-10 relative' />
        </div>

        <h1 className='text-6xl text-center text-slate-100 relative z-10 py-8'>
          BLOG
        </h1>

        <p className='relative z-10 text-center text-slate-200 text-2xl'>
          Read our latest articles
        </p>
      </div>
      <main>
        <section className='container mx-auto lg:max-w-5xl post-list mt-4'>
          <ul>
            {allPosts.nodes.map((post) => (
              <li key={post.slug} className='grid grid-cols-5 gap-4 mb-4'>
                <div className='col-span-2'>
                  <FeaturedImage post={post} />
                </div>
                <div className='col-span-3'>
                  <h2 className='py-4'>
                    <Link
                      href={`/blog/${post.slug}`}
                      className='text-blue-400 text-2xl hover:text-blue-600'
                    >
                      {post.title}
                    </Link>
                  </h2>
                  <div className='py-4'>
                    Published on <Date dateString={post.date} />
                  </div>
                  <div
                    className='text-lg'
                    dangerouslySetInnerHTML={{ __html: post.excerpt }}
                  />
                  <div className='py-4'>
                    Posted under{' '}
                    {post.categories.nodes.map((category) => (
                      <Link
                        className='text-blue-400 hover:text-blue-500'
                        href={`/category/${category.slug}`}
                        key={category.slug}
                      >
                        {category.name}
                      </Link>
                    ))}
                  </div>
                </div>
              </li>
            ))}
          </ul>
        </section>
      </main>
      <SiteFooter />
    </>
  );
}
```

## サムネイル画像コンポーネント

### components/FeaturedImage.tsx

```tsx
import { PostNode } from '@/types/post';
import Image from 'next/image';
import Link from 'next/link';

export default function FeaturedImage({ post }: { post: PostNode }) {
  const defaultFeaturedImage = '/next.svg';
  const defaultWidth = 300;
  const defaultHeight = 200;

  let img = {
    src: defaultFeaturedImage,
    width: defaultWidth,
    height: defaultHeight,
  };

  if (post.featuredImage) {
    let size = post.featuredImage.node.mediaDetails.sizes[0];
    img = {
      src: size.sourceUrl,
      width: Number(size.width),
      height: Number(size.height),
    };
  }

  return (
    <Link href={`/blog/${post.slug}`}>
      <Image
        {...img}
        alt={post.title}
        className='h-full object-cover rounded-xl'
      />
    </Link>
  );
}
```

## 画像を参照するときエラーになる場合

- 設定ファイルにlocalhostを登録する

### next.config.js

```js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'http',
        hostname: 'localhost',
        port: '8000',
        pathname: '/**',
      },
    ],
  },
};
```

## フッターコンポーネント

### components/SiteFooter.tsx

```tsx
export default function SiteFooter() {
  return (
    <>
      <footer id='site-footer' className='flex justify-center bg-slate-200'>
        <div className='py-3'>&copy; 2024 CoolNomad</div>
      </footer>
    </>
  );
}
```