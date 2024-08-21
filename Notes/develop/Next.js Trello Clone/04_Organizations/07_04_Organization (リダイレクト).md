## ファイル構成

```ts
app
 └── middleware.ts
```

## ユーザーが Organization に所属している場合、直接 Organization ページにリダイレクトする

- 認証の状態によってリダイレクト先を決める場合は middleware.ts に記述する

### middleware.ts

```ts
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server';
import { NextResponse } from 'next/server';

// 公開ページ（認証が必要ないページ）
const isPublicRoute = createRouteMatcher(['/', '/sign-in(.*)', '/sign-up(.*)']);

export default clerkMiddleware((auth, req) => {
  const { userId, orgId, protect } = auth();

  // 認証済で公開ページにアクセスした場合
  //  組織作成済：/organization/xxxx にリダイレクト
  //  それ以外：/select-org にリダイレクト
  if (userId && isPublicRoute(req)) {
    let path = '/select-org';

    if (orgId) {
      path = `/organization/${orgId}`;
    }

    const orgSelection = new URL(path, req.url);
    return NextResponse.redirect(orgSelection);
  }

  // 未認証で非公開ページにアクセスした場合
  //  公開ページにリダイレクト
  if (!userId && !isPublicRoute(req)) {
    protect();
  }

  // 認証済 + 組織未作成 + select-org以外のページにアクセスした場合
  //  /select-org にリダイレクト
  if (userId && !orgId && req.nextUrl.pathname !== '/select-org') {
    const orgSelection = new URL('/select-org', req.url);
    return NextResponse.redirect(orgSelection);
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

## 環境変数

### .env

```env
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
```