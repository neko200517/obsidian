## ファイル構成

```ts
.
├── app
│   ├── (platform)
│   │   ├── (clerk)
│   │   │   ├── layout.tsx
│   │   │   ├── sign-in
│   │   │   │   └── [[...sign-in]]
│   │   │   │       └── page.tsx
│   │   │   └── sign-up
│   │   │       └── [[...sign-up]]
│   │   │           └── page.tsx
│   │   ├── layout.tsx
│   │   ├── protected
│   │   │   └── page.tsx // テスト用
│   │   └── protected2
│   │       └── page.tsx // テスト用
│   //...
├── middleware.ts
└── .env.local
```

## ライブラリをインストール

```bash
npm install @clerk/nextjs
```

## 環境変数にAPIキーを追加

メニューから API Key を選択。値を.env ファイルに書き込む

![[Pasted image 20240730005308.png]]

### .env.local

```json
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=xxxxxxxxxxxxxx
CLERK_SECRET_KEY=xxxxxxxxxxxxxx
```

## 環境変数にリダイレクト先を追加 


| 変数                                           | 説明                       |
| -------------------------------------------- | ------------------------ |
| NEXT_PUBLIC_CLERK_SIGN_IN_URL                | サインインしていない場合のリダイレクト先を登録  |
| NEXT_PUBLIC_CLERK_SIGN_UP_URL                | サインアップしていない場合のリダイレクト先を登録 |
| NEXT_PUBLIC_CLERK_SIGN_IN_FORCE_REDIRECT_URL | サインインした場合のリダイレクト先を登録     |
| NEXT_PUBLIC_CLERK_SIGN_UP_FORCE_REDIRECT_URL | サインアップした場合のリダイレクト先を登録    |

### .env.local 

```json
//...
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_SIGN_IN_FORCE_REDIRECT_URL=/select-org
NEXT_PUBLIC_CLERK_SIGN_UP_FORCE_REDIRECT_URL=/select-org
```

## middlewareの作成 

- /, /sign-in, /sign-up 以外のページを認証でプロテクト
- サインインしていない場合、サインインページにリダイレクトする

### middleware.ts

```tsx
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server';

const isPublicRoute = createRouteMatcher(['/', '/sign-in(.*)', '/sign-up(.*)']);

export default clerkMiddleware((auth, req) => {
  if (!isPublicRoute(req)) {
    auth().protect();
  }
});

export const config = {
  matcher: [
    // Skip Next.js internals and all static files, unless found in search params
    '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
    // Always run for API routes
    '/(api|trpc)(.*)',
  ],
};
```

### 認証テスト用の設定

- createRouteMatcher()
	- パスが /protected に適合するurlを認証状態でブロックする
	- 以下の例の場合 http://localhost:3000/protected にアクセスした場合、認証されていない場合サインイン、サインアウトページにリダイレクト

```ts
const isProtectedRoutes = createRouteMatcher(['/protected(.*)']);

export default clerkMiddleware((auth, req) => {
  if (isProtectedRoutes(req)) auth().protect();
});
```

## Platform の共通レイアウトの作成 

- UserButton で afterSignOutUrlプロパティが非推奨になったため、ClerkProvide の afterSignOutUrl を使用すること

### app/(platform)/layout.tsx

```tsx
import { ClerkProvider } from '@clerk/nextjs';

export default function PlatformLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return <ClerkProvider afterSignOutUrl='/'>{children}</ClerkProvider>;
}
```

## Clerk の共通レイアウトの作成 

### app/(platform)/(clerk)/layout.tsx

```tsx
import React from 'react';

export default function ClerkLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className='flex justify-center items-center h-full'>{children}</div>
  );
}
```

## サインインページの作成 

### app/(platform)/(clerk)/sign-in/\[\[...sign-in]]/page.tsx

```tsx
import { SignIn } from '@clerk/nextjs';

export default function Page() {
  return <SignIn />;
}
```

## サインアップページの作成 

### app/(platform)/(clerk)/sign-up/\[\[...sign-up]]/page.tsx

```tsx
import { SignUp } from '@clerk/nextjs';

export default function Page() {
  return <SignUp />;
}
```

## 検証用ページの作成（サーバーサイド）

- UserButtonコンポーネント
	- ユーザー情報、サインアウトなどのボタンを表示
- currentUser()
	- user情報の取得
- auth()
	- userIdの取得

### app/(platform)/protected/page.tsx

```tsx
import { UserButton } from '@clerk/nextjs';
import { auth, currentUser } from '@clerk/nextjs/server';

export default async function ProtectedPage() {
  const user = await currentUser();
  const { userId } = auth();

  return (
    <div>
      <div className='flex gap-x-2 items-center'>
        <UserButton afterSwitchSessionUrl='/' />
        {user?.firstName} {user?.lastName} (id: {userId})
      </div>
    </div>
  );
}
```

## 検証用ページの作成（クライアントサイド）

- UserButtonコンポーネント
	- ユーザー情報、サインアウトなどのボタンを表示
- useUser()
	- user情報の取得
- useAuth()
	- userIdの取得

### app/(platform)/protected2/page.tsx 

```tsx
'use client';

import { UserButton, useAuth, useUser } from '@clerk/nextjs';

export default function Protected2Page() {
  const { user } = useUser();
  const { userId } = useAuth();

  return (
    <div>
      <div className='flex gap-x-2 items-center'>
        <UserButton afterSwitchSessionUrl='/' />
        {user?.firstName} {user?.lastName} (id: {userId})
      </div>
    </div>
  );
}
```