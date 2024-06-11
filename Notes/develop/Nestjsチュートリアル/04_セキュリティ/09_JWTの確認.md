---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - セキュリティ
aliases:
  - <% tp.file.title %>
---
## JWTトークンを複合化する

1. https://jwt.io にアクセスする
2. Debugger の Encoded にアクセストークンを貼り付ける
3. Decorded を確認する

### Encoded 

```json
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjVkZDA3MDg0LTk2MmEtNDA4YS04MjI0LThlZDRjODNhMDUwNSIsInVzZXJuYW1lIjoidXNlcjAyIiwiaWF0IjoxNzE3ODY3MTQ1LCJleHAiOjE3MTc4NzA3NDV9.syitrSVwAsP02QUTot-xPADSDosBYu_z_TZTYGvlckM
```

## 出力を確認する

### HEADER

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### PAYLOAD

```json
{
  "id": "5dd07084-962a-408a-8224-8ed4c83a0505",
  "username": "user02",
  "iat": 1717866437, // 有効期限（開始）
  "exp": 1717870037  // 有効期限（終了）+3600
}
```

### VERIFY SIGNATURE

```ts
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),  
  secret123
)
```