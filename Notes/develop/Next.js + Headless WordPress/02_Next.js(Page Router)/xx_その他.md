## 環境の切り替え

以下の3つのファイルを作成する

- .env.____local
	- For Docker
- .env.____prod
	- For Server
- .env.local

.env.localのみNext.jsが読み込む仕組みを利用して、それぞれの環境変数を.env.localにコピーすることで環境変数の切り替えを行う

### package.json

```json
{
  //...
  "scripts": {
    "dev:local": "cat .env._local > .env.local && next dev",
    "dev": "cat .env._prod > .env.local && next dev",
    "build:local": "cat .env._local > .env.local && next build",
    "build": "cat .env._prod > .env.local && next build",
    "start": "next start",
    "lint": "next lint"
  },
  //...
}
```

## 環境変数の仕様

クライアントが使用する環境変数はNEXT_PUBLIC_xxxという接頭語が必要

```ts
# Wordpress
NEXT_PUBLIC_WP_BASE_URL=http://localhost:8000
NEXT_PUBLIC_WP_GRAPHQL_URL=http://localhost:8000/graphql
NEXT_PUBLIC_WP_AUTH_REFRESH_TOKEN=xxxxxxxxxxxxxxxxxx
NEXT_PUBLIC_REVALIDATION_SECRET=secret_key

# Storage
NEXT_PUBLIC_STORAGE_BASE_URL=http://localhost:8000

# Frontend
NEXT_PUBLIC_FRONT_BASE_URL=http://localhost:3000
```