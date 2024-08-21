## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   └── organization
│   │   │       ├── [organizationId]
│   │   │       │   └── settings
│   │   │       │       └── [[...settings]]
│   │   │       │           └── page.tsx
```

## Organization Settings を作成する

### app/(platform)/(dashboard)/organization/\[organizationId]/settings/\[\[...settings]]/page.tsx

```tsx
import { OrganizationProfile } from '@clerk/nextjs';

export default function SettingsPage() {
  return (
    <div className='w-full'>
      <OrganizationProfile
        appearance={{
          elements: {
            rootBox: {
              boxShadow: 'none',
              width: '100%',
            },
            cardBox: {
              boxShadow: 'none',
              border: '1px solid #e5e5e5',
              width: '100%',
            },
          },
        }}
      />
    </div>
  );
}
```