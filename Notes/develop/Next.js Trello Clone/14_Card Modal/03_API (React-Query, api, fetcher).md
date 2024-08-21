## React-Query の導入

```bash
npm i @tanstack/react-query
```

## ドキュメント

https://tanstack.com/query/latest/docs/framework/react/overview

## カードタイトル取得用のAPIを作成する

### app/api/cards/\[cardId]/route.ts

```ts
import { auth } from '@clerk/nextjs/server';
import { NextResponse } from 'next/server';

import { db } from '@/lib/db';

export async function GET(
  req: Request,
  { params }: { params: { cardId: string } }
) {
  try {
    const { userId, orgId } = auth();

    if (!userId || !orgId) {
      return new NextResponse('Unauthorized', { status: 401 });
    }

    const card = await db.card.findUnique({
      where: {
        id: params.cardId,
        list: {
          board: {
            orgId,
          },
        },
      },
      include: {
        list: {
          select: {
            title: true,
          },
        },
      },
    });

    return NextResponse.json(card);
  } catch (error) {
    return new NextResponse('Internal Error', { status: 500 });
  }
}
```

## フェッチ関数の作成

### lib/fetcher.ts

```ts
export const fetcher = (url: string) => fetch(url).then((res) => res.json());
```
