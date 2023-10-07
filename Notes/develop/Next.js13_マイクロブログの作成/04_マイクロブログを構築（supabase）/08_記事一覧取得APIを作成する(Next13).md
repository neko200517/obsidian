## app/apiディレクトリでAPIを作成する

index.tsの代わりにapp/api/route.tsを作成する。

```sh
cd app
mkdir api
cd api
mkdir blog
touch route.ts
```

## api/blog/route.ts 

記事一覧取得APIを作成する。
関数名は handler の代わりにメソッド名を使用する。

```ts
// api/blog/route.ts

import { supabase } from '@/utils/supabaseClient';
import { NextResponse } from 'next/server';

export async function GET(req: Request) {
  const { data, error } = await supabase.from('posts').select('*');

  if (error) {
    return NextResponse.json(error, { status: 500 });
  }

  return NextResponse.json(data, { status: 200 });
}
```

## APIの呼び方

```ts
export const getAllArticlesSupabase = async () => {
  const res = await fetch(`${API_URL}/api/blog`, { cache: 'no-store' });

  const json = await res.json();

  if (!res.ok) {
    throw new Error(json.message);
  }

  return json;
};
```
