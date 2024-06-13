---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## サーバーを立ち上げる

```bash
npm run start:dev
```

## playgraoundにアクセス

ブラウザでアクセス

```
http://localhost:3000/graphql
```

## クエリの実行

### リクエスト

```ts
query {
  getTasks{
    id
    name
    dueDate
    status
    description
  }
}
```

### レスポンス

```ts
{
  "data": {
    "getTasks": [
      {
        "id": 1,
        "name": "task1",
        "dueDate": "2024-01-01",
        "status": "NOT_STARTED",
        "description": null
      },
    ]
  }
}
```