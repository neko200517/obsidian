## src/app/blogAPI.ts 

詳細ページの取得APIを作成する。

### 全体

```tsx
// src/app/blogAPI.ts

import { notFound } from 'next/navigation';

// 中略

export const getArticle = async (id: string): Promise<Article> => {
  const res = await fetch(`http://localhost:3100/posts/${id}`, {
    next: { revalidate: 60 },
  }); // ISR

  if (res.status === 400) {
    notFound();
  }

  if (!res.ok) {
    throw new Error('error!');
  }

  const article = (await res.json()) as Article;
  return article;
};
```

### ISRで実装する

json-serverでは http://xxxx/{id} の記法でデータが抽出できるのでurlに埋め込む。
またキャッシュの有効期限を60秒に設定したISRで実装する。

```ts
const res = await fetch(`http://localhost:3100/posts/${id}`, {
    next: { revalidate: 60 },
  }); // ISR
```

### NotFound ページを表示する

Next.js13 ではNotFoundを簡単にリダイレクトすることが可能。

```ts
import { notFound } from 'next/navigation';

// 中略

if (res.status === 400) {
  notFound();
}
```
