## api/blog/[id]/route.ts

api/blog/[id]/route.tsを再利用する。

```ts
// api/blog/[id]/route.ts 

import { supabase } from '@/utils/supabaseClient';
import { notFound } from 'next/navigation';
import { NextResponse } from 'next/server';

export async function GET(req: Request) {
	// 省略
}

export async function DELETE(req: Request) {
  const id = req.url.split('/blog/')[1];
  const { error } = await supabase.from('posts').delete().eq('id', id);

  if (error) {
    return NextResponse.json(error, { status: 500 });
  }

  return NextResponse.json({ message: 'Success' }, { status: 200 });
}
```

## APIの呼び方

```ts
export const deleteArticleSupabase = async (id: string): Promise<Response> => {
  const res = await fetch(`${API_URL}/api/blog/${id}`, {
    method: 'DELETE',
  });

  const json = await res.json();

  if (!res.ok) {
    throw new Error(json.message);
  }

  return json;
};
```
