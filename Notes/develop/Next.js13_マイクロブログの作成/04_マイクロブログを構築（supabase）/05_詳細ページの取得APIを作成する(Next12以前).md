## pages/api/[id].ts

pages/api/[id].ts を作成する。

```sh
touch pages/api/[id].ts
```

## 記事取得APIを作成

idは req.query で取得。
eq で id をフィルタリングし、single で単一クエリを取得する。

```ts
// pages/api/[id].ts

import { supabase } from '@/utils/supabaseClient';
import { NextApiRequest, NextApiResponse } from 'next';
import { notFound } from 'next/navigation';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const { id } = req.query;
  const { data, error } = await supabase
    .from('posts')
    .select('*')
    .eq('id', id)
    .single();

  if (error) {
    return res.status(500).json({ error: error.message });
  }

  if (!data) {
    notFound();
  }

  return res.status(200).json(data);
}

```

## API呼び出し用関数を作成

```ts
export const getArticleSupabase = async (id: string) => {
  const API_URL = process.env.NEXT_PUBLIC_API_URL;
  const res = await fetch(`${API_URL}/api/${id}`, { next: { revalidate: 60 } });
  return await res.json();
};
```

## 呼び出す

```tsx
// 中略
const article = await getArticleSupabase(params.id)
// 中略
```
