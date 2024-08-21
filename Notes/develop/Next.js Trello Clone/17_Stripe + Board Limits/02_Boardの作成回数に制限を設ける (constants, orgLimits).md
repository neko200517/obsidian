## 回数制限の定数を作成する

### constants/boards.ts

```ts
export const MAX_FREE_BOARDS = 5;
```

## orgLimits を作成する

- incrementAvalableCount 
	- Boardの作成回数を加算する
- decrementAvalableCount 
	- Boardの作成回数を減算する
- hasAvailableCount 
	- Boardが作成可能か判定する
- getAvailableCount 
	- 現在の作成回数を取得する

### lib/org-limits.ts

```ts
import { auth } from '@clerk/nextjs/server';

import { db } from '@/lib/db';
import { MAX_FREE_BOARDS } from '@/constants/boards';

export const incrementAvalableCount = async () => {
  const { orgId } = auth();

  if (!orgId) {
    throw new Error('Unauthorized');
  }

  const orgLimit = await db.orgLimit.findUnique({
    where: { orgId },
  });

  if (orgLimit) {
    await db.orgLimit.update({
      where: { orgId },
      data: { count: orgLimit.count + 1 },
    });
  } else {
    await db.orgLimit.create({
      data: { orgId, count: 1 },
    });
  }
};

export const decrementAvalableCount = async () => {
  const { orgId } = auth();

  if (!orgId) {
    throw new Error('Unauthorized');
  }

  const orgLimit = await db.orgLimit.findUnique({
    where: { orgId },
  });

  if (orgLimit) {
    await db.orgLimit.update({
      where: { orgId },
      data: { count: orgLimit.count > 0 ? orgLimit.count - 1 : 0 },
    });
  } else {
    await db.orgLimit.create({
      data: { orgId, count: 1 },
    });
  }
};

export const hasAvailableCount = async () => {
  const { orgId } = auth();

  if (!orgId) {
    throw new Error('Unauthorized');
  }

  const orgLimit = await db.orgLimit.findUnique({
    where: { orgId },
  });

  if (!orgLimit || orgLimit.count < MAX_FREE_BOARDS) {
    return true;
  } else {
    return false;
  }
};

export const getAvailableCount = async () => {
  const { orgId } = auth();

  if (!orgId) {
    return 0;
  }

  const orgLimit = await db.orgLimit.findUnique({
    where: { orgId },
  });

  if (!orgLimit) {
    return 0;
  }

  return orgLimit.count;
};
```