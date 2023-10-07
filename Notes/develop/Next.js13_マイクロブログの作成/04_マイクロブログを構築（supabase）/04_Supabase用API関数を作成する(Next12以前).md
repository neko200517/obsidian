## pages/api/index.ts 

全データ取得APIを作成する。

```sh
mkdir pages
cd pages 
mkdir api
touch index.ts
```

## 記事一覧取得APIを作成

select で取得する項目を指定する。"\**"で全項目。

```ts
// pages/api/index.ts

import { supabase } from '@/utils/supabaseClient';
import { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const { data, error } = await supabase.from('posts').select('*');

  if (error) {
    return res.status(500).json({ error: error.message });
  }

  return res.status(200).json(data);
}
```

## API呼び出し用関数を作成

http://localhost:3000/api を呼び出すとNext.jsの機能で API が呼び出せるようになっている。内部でsupabaseのAPIを呼び出しているのでNext.jsのAPIを経由してsupabseのAPIを呼び出すといった経路でAPIを呼び出す。

```tsx
export const getAllArticlesSupabase = async () => {
  const API_URL = process.env.NEXT_PUBLIC_API_URL;
  const res = await fetch(`${API_URL}/api`, { cache: 'no-store' });
  return await res.json();
};
```
