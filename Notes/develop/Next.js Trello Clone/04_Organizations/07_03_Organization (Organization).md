## ファイル構成

```ts
app
├── (platform)
│   ├── (dashboard)
│   │   ├── _components
│   │   │   └── navbar.tsx
│   │   ├── layout.tsx
│   │   └── organization
│   │       └── [organizationId]
│   │           └── page.tsx
```

## Organization Laytout の作成 

- Navbarは Platform Dashboard 専用のものを使用する

### app/(platform)/(dashboard)/layout.tsx

```tsx
import Navbar from './_components/navbar';

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className='h-full'>
      <Navbar />
      {children}
    </div>
  );
}
```

## SkyUI - Button の色を調整

variant: primaryを追加

### components/ui/button.tsx

```tsx
const buttonVariants = cva(
  //...
  {
    variants: {
      variant: {
        //...
        primary: 'bg-sky-700 text-primary-foreground hover:bg-sky-700/90',
      },
  //...
```

## Organization Navbar の作成

- OrganizationSwitcher 
	- afterCreateOrganizationUrl
		- Organization作成後にリダイレクト
	- afterLeaveOrganizationUrl 
		- Organizationを離脱後にリダイレクト 
	- afterSelectOrganizationUrl
		- Organizationを選択後にリダイレクト

### app/(platform)/(dashboard)/\_components/navbar.tsx

```tsx
import { Plus } from 'lucide-react';
import { OrganizationSwitcher, UserButton } from '@clerk/nextjs';

import Logo from '@/components/logo';
import { Button } from '@/components/ui/button';

export default function Navbar() {
  return (
    <nav className='fixed z-50 top-0 px-4 w-full h-14 border-b shadow-sm bg-white flex items-center'>
      <div className='flex items-center gap-x-4'>
        <div className='hidden md:flex'>
          <Logo />
        </div>
        <Button
          variant='primary'
          size='sm'
          className='rounded-sm hidden md:block h-auto py-1.5 px-2'
        >
          Create
        </Button>
        <Button
          variant='primary'
          size='sm'
          className='rounded-sm block md:hidden'
        >
          <Plus className='h-4 w-4' />
        </Button>
      </div>
      <div className='ml-auto flex items-center gap-x-2'>
        <OrganizationSwitcher
          hidePersonal
          afterCreateOrganizationUrl='/organization/:id'
          afterLeaveOrganizationUrl='/select-org'
          afterSelectOrganizationUrl='/organization/:id'
          appearance={{
            elements: {
              rootBox: {
                display: 'flex',
                justifyContent: 'center',
                alignItems: 'center',
              },
            },
          }}
        />
        <UserButton
          appearance={{
            elements: {
              avatarBox: {
                width: 30,
                height: 30,
              },
            },
          }}
        />
      </div>
    </nav>
  );
}
```

## Organization ページを作成 

### app/(dashboard)/organization/[organizationId]/page.tsx

```tsx
import { auth } from '@clerk/nextjs/server';

export default function OrganizationPage() {
  const { orgId } = auth();

  return (
    <div className='mt-16'>
      <div>orgId: {orgId}</div>
    </div>
  );
}
```

