## ファイル構成

```ts
.
├── actions
│   └── delete-board.ts
```

## Board の削除アクション

### action/delete-board.ts

```ts
'use server';

import { auth } from '@clerk/nextjs/server';
import { revalidatePath } from 'next/cache';

import { db } from '@/lib/db';

export async function deleteBoard(id: string) {
  await db.board.delete({
    where: {
      id,
    },
  });

  revalidatePath(`organization/${auth().orgId}`);
}
```
