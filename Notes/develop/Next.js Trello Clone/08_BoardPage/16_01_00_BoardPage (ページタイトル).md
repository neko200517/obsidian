## lodash のインストール

```bash
npm i lodash
npm i -D @types/lodash
```

## OrganizationIdLayout を修正

- orgSlug の先頭を大文字にしてページタイトルに使う

### app/(platform)/(dashboard)/organization/\[organizationId]/layout.tsx

```tsx
import { startCase } from 'lodash';
import { auth } from '@clerk/nextjs/server';

import OrgControle from './_components/org-control';

export async function generateMetadata() {
  const { orgSlug } = auth();

  return {
    title: startCase(orgSlug || 'organization'),
  };
}

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