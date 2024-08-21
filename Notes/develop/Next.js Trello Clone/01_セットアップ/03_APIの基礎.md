## 基本的なAPIの作成

### ファイル構成

```ts
├── app
│   ├── api
│   │   └── users
│   │       └── route.ts
```

### GET APIの作成 

#### app/api/users/route.ts

```ts
import { NextResponse } from 'next/server';

export function GET() {
  return NextResponse.json({
    hello: 'trello',
  });
}
```

### アクセス方法

http://localhost:3000/api/users