## SubscriptionButton を作成する

- 決済済
	- Manage subscription ボタンが表示される
		- Stripeの請求管理画面に遷移する
- 未決済
	- Update to pro ボタンが表示される
		- ProModal が表示される
			- Stripeの決済画面に遷移する

### app/(platform)/(dashboard)/organization/\[organizationId]/billing/\_components/subscription-button.tsx

```tsx
'use client';

import { toast } from 'sonner';

import { Button } from '@/components/ui/button';
import { stripeRedirect } from '@/actions/stripe-redirect';
import { useAction } from '@/hooks/use-action';
import { useProModal } from '@/hooks/use-pro-modal';

interface SubscriptionButtonProps {
  isPro: boolean;
}

export default function SubscriptionButton({ isPro }: SubscriptionButtonProps) {
  const proModal = useProModal();

  const { execute, isLoading } = useAction(stripeRedirect, {
    onSuccess: (data) => {
      window.location.href = data;
    },
    onError: (error) => {
      toast.error(error);
    },
  });

  const onClick = () => {
    if (isPro) {
      execute({});
    } else {
      proModal.onOpen();
    }
  };

  return (
    <Button variant='primary' onClick={onClick} disabled={isLoading}>
      {isPro ? 'Manage subscription' : 'Upgrade to pro'}
    </Button>
  );
}
```