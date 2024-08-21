## Prisma のインストール

```bash
npm i -D prisma
```

## Prisma の初期化

```bash
npx prisma init
```

## Neon の導入

無料枠のあるSaaS (Serverless PostgreSQL)

https://neon.tech/

## DBの作成

![[Pasted image 20240729163439.png]]

### 設定

| Project name     | trello-clone             |
| ---------------- | ------------------------ |
| Postgres version | 16                       |
| Database name    | trello-clone             |
| Region           | Asia Pacific (Singapore) |


## 接続文字列をコピー

メニューの Dashboard から Connection string をコピー

![[Pasted image 20240729164143.png]]

## .env ファイルに接続文字列をコピー 

### .env 

```json
DATABASE_URL='postgresql://xxxxxxxxxxxxxxxxx'
```

## Prisma スキーマの設定

### prisma/schema.prisma

```ts
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

