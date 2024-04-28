## src/app/tyles.ts

・記事の型を定義

```ts
export type PostType = {
  userId: number;
  id: number;
  title: string;
  body: string;
};
```

## src/app/lib/posts.ts

・jsonplaceholder から記事一覧を取得して json 形式で返す

```ts
import { PostType } from '../types';

const apiUrl = 'https://jsonplaceholder.typicode.com/posts';

export const getAllPostsData = async (): Promise<PostType[]> => {
  const res = await fetch(new URL(apiUrl));

  if (!res.ok) {
    throw new Error('Error!');
  }

  return await res.json();
};
```

## src/app/blog/page.tsx

・posts から取得したブログ一覧を表示

```tsx
import { Metadata } from 'next';
import { getAllPostsData } from '../lib/posts';
import Post from '../components/Post';

export const metadata: Metadata = {
  title: 'Blog',
};

const Blog = async () => {
  const posts = await getAllPostsData();
  return (
    <ul className='m-10'>
      {posts && posts.map((post) => <Post key={post.id} post={post} />)}
    </ul>
  );
};

export default Blog;
```

## src/app/components/Post.tsx

・Post コンポーネント

```tsx
import { PostType } from '../types';

type PostProps = {
  post: PostType;
};

const Post = (props: PostProps) => {
  const { post } = props;
  return (
    <div>
      <span>{post.id}</span>
      {' : '}
      <span className='cursor-pointer text-blue-500 border-b border-blue-500 hover:bg-gray-200'>
        {post.title}
      </span>
    </div>
  );
};

export default Post;
```