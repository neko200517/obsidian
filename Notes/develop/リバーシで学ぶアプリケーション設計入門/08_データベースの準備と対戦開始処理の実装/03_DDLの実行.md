---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - typescript
  - express
  - 設計
  - オブジェクト指向
  - DI
  - 3層アーキテクチャ
  - デザインパターン
aliases:
  - <% tp.file.title %>
---

## DDL を簡単に実行するためにスクリプトを作成

```bash
touch bin/load_ddl.sh
chmod +x bin/load_ddl.sh
```

### /bin/load_ddl.sh

```sh
#!/bin/bash

cat mysql/init.sql | docker-compose exec -T mysql mysql --user=root --password=rootpassword
```

init.sql の出力結果を docker-compose の mysql に渡して SQL を実行

## スクリプトの実行

```bash
bin/load.sh
```
