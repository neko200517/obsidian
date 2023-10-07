## pages/api/create.ts

pages/api/create.ts を作成する。

```sh
touch pages/api/create.ts
```

## 記事作成APIを作成

bodyでオブジェクトを取得。
insertでデータ挿入。

```ts
// pages/api/create.ts

import { supabase } from '@/utils/supabaseClient';
import { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const article = req.body;
  const { data, error } = await supabase
    .from('posts')
    .insert([{ ...article }])
    .select();

  if (error) {
    return res.status(500).json({ error: error.message });
  }

  return res.status(200).json(data);
}
```

## API呼び出し用関数を作成

```ts
export const createArticleSupabase = async (article: PostArticle) => {
  const API_URL = process.env.NEXT_PUBLIC_API_URL;
  const res = await fetch(`${API_URL}/api/create`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(article),
  });
  return await res.json();
};
```

## 呼び出す

```tsx
// 中略
await createArticleSupabase(article)
// 中略
```
