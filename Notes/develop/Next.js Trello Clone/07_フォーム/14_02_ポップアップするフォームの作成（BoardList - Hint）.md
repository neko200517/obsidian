
## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (dashboard)
│   │   │   └── organization
│   │   │       ├── [organizationId]
│   │   │       │   ├── _components
│   │   │       │   │   └── board-list.tsx
│   │   │       │   ├── page.tsx
//...
├── components
│   ├── hint.tsx
```

## ShadUI - Tooltip のインストール

- ツールチップを追加

```bash
npx shadcn-ui@latest add tooltip
```

## Hint を作成する

- マウスオーバーするとポップアップする Tooptip を作成

### components/hint.tsx

```tsx
import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger,
} from '@/components/ui/tooltip';

interface HintProps {
  children: React.ReactNode;
  description: string;
  side?: 'left' | 'right' | 'top' | 'bottom';
  sideOffest?: number;
}

export default function Hint({
  children,
  description,
  side = 'bottom',
  sideOffest = 0,
}: HintProps) {
  return (
    <TooltipProvider>
      <Tooltip delayDuration={0}>
        <TooltipTrigger>{children}</TooltipTrigger>
        <TooltipContent
          sideOffset={sideOffest}
          side={side}
          className='text-xs max-w-[220px] break-words'
        >
          {description}
        </TooltipContent>
      </Tooltip>
    </TooltipProvider>
  );
}
```

## BoardList を作成する

### app/(platform)/(dashboard)/organization/\[organizationId]/\_components/board-list.tsx

```tsx
import { HelpCircle, User2 } from 'lucide-react';

import Hint from '@/components/hint';

export default function BoardList() {
  return (
    <div className='space-y-4'>
      <div className='flex items-center font-semibold text-lg text-neutral-700'>
        <User2 className='h-6 w-6 mr-2' />
        Your boards
      </div>
      <div className='grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-4 gap-4 '>
        <div
          role='button'
          className='aspect-video relative h-full w-full bg-muted rounded-sm flex flex-col gap-y-1 items-center justify-center hover:opacity-75 transition'
        >
          <p className='text-sm'>Create new board</p>
          <span className='text-xs'>5 remaing</span>
          <Hint
            sideOffest={40}
            description={
              'Free Wordkspaces can have up to 5 open boards. For unlimited boards upgrade this workspace.'
            }
          >
            <HelpCircle className='absolute bottom-2 right-2 h-[14px] w-[14px]' />
          </Hint>
        </div>
      </div>
    </div>
  );
}
```

## OrganizationIdPage を修正する

- BoardListを組み込む

### app/(platform)/(dashboard)/organization/\[organizationId]/page.tsx

```tsx
import { Separator } from '@/components/ui/separator';

import Info from './_components/info';
import BoardList from './_components/board-list';

export default async function OrganizationIdPage() {
  return (
    <div className='w-full mb-20'>
      <Info />
      <Separator className='my-4' />
      <div className='px-2 md:px-4'>
        <BoardList />
      </div>
    </div>
  );
}
```
