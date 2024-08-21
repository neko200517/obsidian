## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   └── organization
│   │   │       ├── [organizationId]
│   │   │       │   ├── board.tsx
│   │   │       │   ├── form-button.tsx
│   │   │       │   ├── form-input.tsx
│   │   │       │   ├── form.tsx
│   │   │       │   ├── page.tsx
```

## ShadUI - Input のインストール 

```bash
npx shadcn-ui@latest add input
```

## OrganizationPage の作成

### app/(platform)/(dashboard)/organization/\[organizationId]/page.tsx

```tsx
import { db } from '@/lib/db';

import Board from './board';
import Form from './form';

export default async function OrganizationPage() {
  const boards = await db.board.findMany();

  return (
    <div className='flex flex-col space-y-4'>
      <Form />
      <div className='space-y-2'>
        {boards.map((board) => (
          <Board key={board.id} title={board.title} id={board.id} />
        ))}
      </div>
    </div>
  );
}
```

## Board の作成 

- bind
	- Server Action を form 要素の action に割り当てるために実行。第2引数に id を渡す

### app/(platform)/(dashboard)/organization/\[organizationId]/board.tsx

```tsx
import { deleteBoard } from '@/actions/delete-board';
import FormDelete from './form-delete';

interface BoardProps {
  title: string;
  id: string;
}

export default function Board({ title, id }: BoardProps) {
  const deleteBoardWithId = deleteBoard.bind(null, id);

  return (
    <form action={deleteBoardWithId} className='flex items-center gap-x-2'>
      <p>Board name: {title}</p>
      <FormDelete />
    </form>
  );
}
```

## Form の作成 

- useFormState 
	- 第一引数にアクション、第二引数に初期データ
	- state に action の実行結果（エラーメッセージ）を受け取る
	- form action に dispatch を割り当てる

### app/(platform)/(dashboard)/organization/\[organizationId]/form.tsx

```tsx
'use client';

import { useFormState } from 'react-dom';

import { create } from '@/actions/create-board';
import FormInput from './form-input';
import FormButton from './form-button';

export default function Form() {
  const initialState = { message: null, errors: {} };
  const [state, dispath] = useFormState(create, initialState);

  return (
    <form action={dispath}>
      <div className='flex flex-col space-y-2'>
        <FormInput errors={state?.errors} />
      </div>
      <FormButton />
    </form>
  );
}
```

## FormInput の作成 

- useFormStatus 
	- pending (処理中) の状態を受け取ってフォームコントロールの disabled に渡す

### app/(platform)/(dashboard)/organization/\[organizationId]/form-input.tsx

```tsx
'use client';

import { useFormStatus } from 'react-dom';
import { Input } from '@/components/ui/input';

interface FormInputProps {
  errors?: {
    title?: string[];
  };
}

export default function FormInput({ errors }: FormInputProps) {
  const { pending } = useFormStatus();

  return (
    <div>
      <Input
        id='title'
        name='title'
        type='text'
        placeholder='Enter a board title'
        required
        disabled={pending}
      />
      {errors ? (
        <div>
          {errors.title?.map((error: string) => (
            <p key={error} className='text-rose-500'>
              {error}
            </p>
          ))}
        </div>
      ) : null}
    </div>
  );
}
```

## FormButton の作成

### app/(platform)/(dashboard)/organization/\[organizationId]/form-button.tsx

```tsx
'use client';

import { Button } from '@/components/ui/button';
import { useFormStatus } from 'react-dom';

export default function FormButton() {
  const { pending } = useFormStatus();

  return (
    <Button type='submit' disabled={pending}>
      Submit
    </Button>
  );
}
```