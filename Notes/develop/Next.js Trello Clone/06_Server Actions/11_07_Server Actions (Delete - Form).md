## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   └── organization
│   │   │       ├── [organizationId]
│   │   │       │   ├── form-delete.tsx
```

## FormDelete の作成

### app/(platform)/(dashboard)/organization/\[organizationId]/form-delete.tsx

```ts
'use client';

import { Button } from '@/components/ui/button';
import { useFormStatus } from 'react-dom';

export default function FormDelete() {
  const { pending } = useFormStatus();

  return (
    <Button type='submit' variant='destructive' size='sm' disabled={pending}>
      Delete
    </Button>
  );
}
```