---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - postgresql
  - tips
aliases:
  - <% tp.file.title %>
---
## 概要

ローカルにインストールした PostgreSQL を使うための手順。

## ロールを作成する

以下のロールを作成する

- ユーザー名：root 
- パスワード：root

```sql
-- スーパーユーザでログイン
psql -U postgres

CREATE ROLE root WITH LOGIN PASSWORD 'root';
```

## CREATE TABLE 可能な権限を付与する

```sql
GRANT CREATE ON SCHEMA public TO root;
```

## CRUD 操作可能な権限を付与する

```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO root;
```

## 将来作成されるテーブルに対するデフォルト権限の設定

```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO root;
```