## api/blog/[id]/route.ts

api/blog/[id]/route.tsを作成する。
req.url で現在の url を取得し、split関数で id を取得する。

```ts
// api/blog/[id]/route.ts 

import { supabase } from '@/utils/supabaseClient';
import { notFound } from 'next/navigation';
import { NextResponse } from 'next/server';

export async function GET(req: Request) {
  const id = req.url.split('/blog/')[1];
  const { data, error } = await supabase
    .from('posts')
    .select('*')
    .eq('id', id)
    .single();

  if (error) {
    return NextResponse.json(error, { status: 500 });
  }

  if (!data) {
    notFound();
  }

  return NextResponse.json(data, { status: 200 });
}
```

## APIの呼び方

```ts
export const getArticleSupabase = async (id: string) => {
  const res = await fetch(`${API_URL}/api/blog/${id}`, {
    next: { revalidate: 60 },
  });

  const json = await res.json();

  if (!res.ok) {
    throw new Error(json.message);
  }

  return json;
};
```
