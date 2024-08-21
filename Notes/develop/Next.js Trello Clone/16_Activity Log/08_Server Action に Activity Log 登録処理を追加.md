## 変更箇所

```ts
app
├── actions
│   ├── copy-card
│   │   └── index.ts
│   ├── copy-list
│   │   └── index.ts
│   ├── create-board
│   │   └── index.ts
│   ├── create-card
│   │   └── index.ts
│   ├── create-list
│   │   └── index.ts
│   ├── delete-board
│   │   └── index.ts
│   ├── delete-card
│   │   └── index.ts
│   ├── delete-list
│   │   └── index.ts
│   ├── update-board
│   │   └── index.ts
│   ├── update-card
│   │   └── index.ts
│   ├── update-list
│   │   └── index.ts
//...
```

## 各種処理を追加

- 修正箇所が多いため上記ファイルに以下の処理を追加する
	- 既存のCRUD処理成功後に createAuditLog を追加
		- テーブルによって entityType を変更 
			- BOARD, LIST, CARD
		- 処理の種類によって action を変更
			- CREATE, UPDATE, DELETE

### サンプル

```ts
//...
import { createAuditLog } from '@/lib/create-audit-log';
import { ACTION, ENTITY_TYPE } from '@prisma/client';

const handler = async (data: InputType): Promise<RetrunType> => {
  //...
  try {
    //...
    await createAuditLog({
      entityId: card.id,
      entityTitle: card.title,
      entityType: ENTITY_TYPE.CARD,
      action: ACTION.CREATE,
    });
  } catch {
    //...
  }
  //...
};
//...
```
