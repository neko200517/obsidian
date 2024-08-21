## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   └── organization
│   │   │       ├── [organizationId]
│   │   │       │   └── page.tsx
```

## DBに登録されるか確認（仮のプログラム）

フォームに文字列を入力しEnterを押下するとDBにレコードが登録される

### app/(platform)/(dashboard)/organization/\[organizationId]/page.tsx

```tsx
import { db } from '@/lib/db';

export default function OrganizationPage() {
  async function create(formData: FormData) {
    'use server';

    const title = formData.get('title') as string;

    await db.board.create({
      data: {
        title,
      },
    });
  }

  return (
    <div>
      <form action={create}>
        <input
          id='title'
          name='title'
          type='text'
          placeholder='Enter a board title'
          className='border-black border p-1'
          required
        />
      </form>
    </div>
  );
}
```

## DBの確認

```bash
npx prisma studio
```

http://localhost:5555 にアクセスすると追加されたレコードが確認可能