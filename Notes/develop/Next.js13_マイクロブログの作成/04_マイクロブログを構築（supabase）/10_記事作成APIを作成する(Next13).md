## api/blog/route.ts

api/blog/route.ts を再利用する。

```ts
// api/blog/route.ts

import { supabase } from '@/utils/supabaseClient';
import { notFound } from 'next/navigation';
import { NextResponse } from 'next/server';

export async function GET(req: Request) {
  // 省略
}

export async function POST(req: Request) {
  const article = await req.json();
  const { data, error } = await supabase
    .from('posts')
    .insert([{ ...article }])
    .select();

  if (error) {
    return NextResponse.json(error, { status: 500 });
  }

  return NextResponse.json(data, { status: 201 });
}
```

## APIの呼び方

```ts
export const createArticleSupabase = async (article: PostArticle) => {
  const res = await fetch(`${API_URL}/api/blog`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(article),
  });

  const json = await res.json();

  if (!res.ok) {
    throw new Error(json.message);
  }

  return json;
};
```
