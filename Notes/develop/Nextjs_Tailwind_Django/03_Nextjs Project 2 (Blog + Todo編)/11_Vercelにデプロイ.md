## 手順

1. github にソースコードを push
2. push したリポジトリを Vercel で import
3. 環境変数を設定する（以下を参照）
4. デプロイ

## 環境変数

.env.local に記載されている環境変数を Vercel の Environment Variables に設定する

```env
NEXT_PUBLIC_RESTAPI_URL=https://xxxxx.onrender.com
```

## バックエンドの修正

Render.com にデプロイした API の設定を変更して再度デプロイする

### rest_api/settings.py

```python
CORS_ORIGIN_WHITELIST = [
  'http://localhost:3000',
  'http://127.0.0.1:3000',
  'https://nextjs-basics-02-frontend.vercel.app', ← 末尾に / があったら削除しておく
]
```

## URL

https://nextjs-basics-02-frontend.vercel.app/