## create-next-app

TypeScript, Tailwind を有効にしてプロジェクトを作成

```bash
mkdir nextjs-basics-ts-01
cd nextjs-basics-ts-01
npx create-next-app@latest .
```

## Tailwind Cheet Sheet

CSS と Tailwind の相関が知りたい場合、以下のチートシートが役立つ

https://nerdcave.com/tailwind-cheat-sheet

## アイコン素材

https://heroicons.com/

## src/app/page.tsx

トップページ

```tsx
import Blog from './components/blog';
import Contact from './components/Contact';

export default function Home() {
  return (
    <div className='min-h-screen py-0 px-8 flex flex-col justify-center items-center'>
      <h1>Hello</h1>
    </div>
  );
}
```

## src/app/blog/page.tsx

Blogページ

```tsx
const Blog = () => {
  return <div>Blog</div>;
};

export default Blog;
```

## src/app/contact/page.tsx

Contactページ

```tsx
const Contact = () => {
  return <div>Contact</div>;
};

export default Contact;
```