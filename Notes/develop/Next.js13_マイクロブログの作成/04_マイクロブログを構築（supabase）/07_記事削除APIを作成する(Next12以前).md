## pages/api/delete/[id].ts

pages/api/delete/[id].ts を作成する。

```sh
touch pages/api/delete/[id].ts
```

## 記事削除APIを作成

methodで条件分岐するようにする。
deleteで削除。eqで条件を指定。

```ts
// pages/api/delete/[id].ts

import { supabase } from '@/utils/supabaseClient';
import { NextApiRequest, NextApiResponse } from 'next';
import { notFound } from 'next/navigation';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  switch (req.method) {
    case 'GET':
      return getArticle(req, res);
    case 'DELETE':
      return deleteArticle(req, res);
    default:
      return [];
  }
}

const getArticle = async (req: NextApiRequest, res: NextApiResponse) => {
  // 省略
};

const deleteArticle = async (req: NextApiRequest, res: NextApiResponse) => {
  const { id } = req.query;
  const { error: deleteError } = await supabase
    .from('posts')
    .delete()
    .eq('id', id);

  if (deleteError) {
    return res.status(500).json({ error: deleteError.message });
  }

  return res.status(200).json({ message: 'Sucess Delete' });
};
```

## API呼び出し用関数を作成

```ts
export const deleteArticleSupabase = async (id: string): Promise<Response> => {
  const res = await fetch(`${API_URL}/api/delete/${id}`, {
    method: 'DELETE',
  });

  if (!res.ok) {
    throw new Error(res.statusText);
  }

  return res;
};
```

## 呼び出す

```tsx
// 中略
await deleteArticleSupabase(id)
// 中略
```
