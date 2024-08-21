## ファイル構成

- (marketing)フォルダ配下の_componentsフォルダにコンポーネントを作成してmarketing専用のコンポーネントとして管理する

```ts
├── app
│   ├── (marketing)
│   │   ├── _components
│   │   │   ├── footer.tsx
│   │   │   └── navbar.tsx
```

## Navbar を作成

### app/(marketing)/\_components/navbar.tsx

```tsx
import Link from 'next/link';

import Logo from '@/components/logo';
import { Button } from '@/components/ui/button';

export default function Navbar() {
  return (
    <div className='fixed top-0 w-full h-14 px-4 border-b shadow-sm bg-white flex items-center'>
      <div className='md:max-w-screen-2xl mx-auto flex items-center w-full justify-between'>
        <Logo />
        <div className='space-x-4 md:block md:w-auto flex items-center justify-between w-full'>
          <Button size='sm' variant='outline' asChild>
            <Link href='/sign-in'>Login</Link>
          </Button>
          <Button size='sm' asChild>
            <Link href='/sign-up'>Get Taskify for free</Link>
          </Button>
        </div>
      </div>
    </div>
  );
}
```

## Footer を作成 

### app/(marketing)/\_components/footer.tsx

```tsx
import Link from 'next/link';

import Logo from '@/components/logo';
import { Button } from '@/components/ui/button';

export default function Footer() {
  return (
    <div className='fixed bottom-0 w-full p-4 border-t bg-slate-100'>
      <div className='md:max-w-screen-2xl mx-auto flex items-center w-full justify-between'>
        <Logo />
        <div className='space-x-4 md:block md:w-auto flex items-center justify-between w-full'>
          <Button size='sm' variant='ghost'>
            Privaciy Policy
          </Button>
          <Button size='sm' variant='ghost'>
            Terms fo Service
          </Button>
        </div>
      </div>
    </div>
  );
}
```