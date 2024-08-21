## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   ├── layout.tsx
│   │   │   └── organization
│   │   │       ├── [organizationId]
│   │   │       │   ├── _components
│   │   │       │   │   └── org-control.tsx
│   │   │       │   ├── layout.tsx
│   │   │       │   └── page.tsx
│   │   │       └── layout.tsx
```

## OrganizationLayout を作成

- サイドバーとメインコンテンツを配置

### app/(platform)/(dashboard)/organization/layout.tsx

```tsx
export default function OrganizationLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <main className='pt-20 md:pt-24 px-4 max-w-6xl 2xl:max-w-screen-xl mx-auto'>
      <div className='flex gap-x-7'>
        <div className='w-64 shrink-0 hidden md:block'>{/* Sidebar */}</div>
        {children}
      </div>
    </main>
  );
}
```

## OrganizationIdLayout を作成

### app/(platform)/(dashboard)/organization/\[organizationId\]/layout.tsx

```tsx
import OrgControle from './_components/org-control';

export default function OrganizationIdLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      <OrgControle />
      {children}
    </>
  );
}
```

## OrgControle を作成 

- Organizationを切り替えたらActiveにする

### app/(platform)/(dashboard)/organization/\[organizationId\]/\_components/org-control.tsx

```tsx
'use client';

import { useEffect } from 'react';
import { useOrganizationList } from '@clerk/nextjs';
import { useParams } from 'next/navigation';

export default function OrgControle() {
  const params = useParams();
  const { setActive } = useOrganizationList();

  useEffect(() => {
    if (!setActive) return;

    setActive({
      organization: params.organizationId as string,
    });
  }, [setActive, params.organizationId]);

  return null;
}
```