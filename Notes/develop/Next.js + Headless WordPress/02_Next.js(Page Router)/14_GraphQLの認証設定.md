## 概要

GraphQLからリフレッシュトークンを取得する方法を学ぶ

## Wordpressにプラグインを追加

### 手動でプラグインを追加

以下のリポジトリをzipにしてプラグイン→アップロードでプラグインを追加

https://github.com/wp-graphql/wp-graphql-jwt-authentication 

### wp-config.phpを修正

このサイトでソルトキーを取得。以下のwp-config.phpに値を書き込む

https://api.wordpress.org/secret-key/1.1/salt/

- Azureの場合
	- /home/site/wwwroot/wp-config.php
- それ以外の場合 
	- /var/www/html/wp-config.php

```php
define( 'GRAPHQL_JWT_AUTH_SECRET_KEY', 'xxxxxxxxxxxxxxx' );
```

## リフレッシュトークンを取得 

GraphQL IDE で以下のMutationを実行

```ts
mutation LoginMutation {
  login(
    input: {password: "xxxxxx", username: "xxxxxx", clientMutationId: "uniqueId"}
  ) {
    refreshToken
  }
}
```

## リフレッシュトークンを環境変数に書き込む 

```ts
//...
NEXT_PUBLIC_WP_AUTH_REFRESH_TOKEN=xxxxxxxxxx
```

## クライアントコードにトークンを追加する

### lib/graphqlRequest.ts

```ts
type RequestHeader = {
  'Content-Type': string;
  Authorization?: string;
};

export default async function graphqlRequest(query: {
  query: string;
}): Promise<{ data: any }> {
  const url = process.env.NEXT_PUBLIC_WP_GRAPHQL_URL!;
  let headers: RequestHeader = { 'Content-Type': 'application/json' };

  if (process.env.NEXT_PUBLIC_WP_AUTH_REFRESH_TOKEN) {
    headers = {
      ...headers,
      Authorization: `Bearer ${process.env.NEXT_PUBLIC_WP_AUTH_REFRESH_TOKEN}`,
    };
  }

  const res = await fetch(url, {
    headers,
    method: 'POST',
    body: JSON.stringify(query),
  });

  const reJson = await res.json();
  return reJson;
}
```

## 検証

1. WordpressのGraphQLプラグイン
	1. →Settings 
	2. →Restrict Endpoint to Authenticated Users にチェックを入れる
	3. →変更を保存
2. Next.jsからデータが読み込めるか確認する