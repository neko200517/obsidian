---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - セキュリティ
aliases:
  - <% tp.file.title %>
---
## Module, Controller, Serviceの作成

```bash
npx nest g module auth
npx nest g controller auth --no-spec
npx nest g service auth --no-spec
```

## フォルダ構成

```ts
├── src
│   ├── auth
│   │   ├── auth.controller.ts
│   │   ├── auth.module.ts
│   │   └── auth.service.ts
```