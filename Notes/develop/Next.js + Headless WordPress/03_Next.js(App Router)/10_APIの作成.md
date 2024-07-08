## コメント投稿API

### app/api/comment/route.ts

```ts
import { NextRequest, NextResponse } from 'next/server';
import { createComment } from '@/lib/comments';

type RequestData = {
  author: string;
  authorEmail: string;
  content: string;
  postId: number;
};

export async function POST(req: NextRequest) {
  const body = (await req.json()) as RequestData;

  if (
    !body.author ||
    !body.authorEmail ||
    !body.content ||
    body.postId === undefined
  ) {
    return NextResponse.json(
      { message: 'Required fields missing', body },
      { status: 400 }
    );
  }

  const resJson = await createComment(body);

  if (resJson.errors) {
    return NextResponse.json(
      { message: resJson.errors[0].message, body },
      { status: 401 }
    );
  } else if (
    resJson.data.createComment !== null &&
    resJson.data.createComment.success === true
  ) {
    return NextResponse.json({ message: 'Your comment is awaiting approval' });
  }

  return NextResponse.json({ message: 'Some error occurred' }, { status: 500 });
}
```

## 問い合わせ投稿API

### app/api/form/route.ts

```ts
import { NextRequest, NextResponse } from 'next/server';

type RequestData = {
  firstName: string;
  email: string;
  message: string;
};

export async function POST(req: NextRequest) {
  const body = (await req.json()) as RequestData;

  if (!body.firstName || !body.email || !body.message) {
    return NextResponse.json(
      { message: 'first name, email, and message fields are required!' },
      { status: 400 }
    );
  }

  return NextResponse.json({ message: 'form submitted successfully' });
}
```

## Ondemand-ISR API

### app/api/revalidate/route.ts

```ts
import { NextRequest, NextResponse } from 'next/server';
import { revalidatePath } from 'next/cache';

export async function GET(req: NextRequest) {
  const type = req.nextUrl.searchParams.get('type');
  const secret = req.nextUrl.searchParams.get('secret');
  const slug = req.nextUrl.searchParams.get('slug');
  let path = '';

  switch (type) {
    case 'post':
      path = '/blog/' + slug;
      break;
    case 'page':
      path = '/' + slug;
      break;
    case 'home':
      path = '/blog';
      break;
  }

  if (secret !== process.env.NEXT_PUBLIC_REVALIDATION_SECRET) {
    return NextResponse.json({ message: 'Invalid token' }, { status: 401 });
  }

  try {
    await revalidatePath(path);
    return NextResponse.json({
      revalidated: true,
      path: path,
      time: Date.now(),
    });
  } catch (err: any) {
    return NextResponse.json(
      {
        revalidated: false,
        message: err.message,
      },
      { status: 500 }
    );
  }
}
```