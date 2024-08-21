## Query Provider を作成 

### components/providers/query-provider.tsx

```tsx
'use client';

import React, { useState } from 'react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

export default function QueryProvider({
  children,
}: {
  children: React.ReactNode;
}) {
  const [queryClient] = useState(() => new QueryClient());

  return (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  );
}
```

## PlatformLayout に組み込む

- Platform配下で呼び出せるように組み込む

### app/(platform)/layout.tsx

```tsx
import { ClerkProvider } from '@clerk/nextjs';
import { Toaster } from '@/components/ui/sonner';

import ModalProvider from '@/components/providers/modal-provider';
import QueryProvider from '@/components/providers/query-provider';

export default function PlatformLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      <ClerkProvider afterSignOutUrl='/'>
        <QueryProvider>
          <Toaster />
          <ModalProvider />
          {children}
        </QueryProvider>
      </ClerkProvider>
    </>
  );
}
```