## DDLを簡単に実行するためにスクリプトを作成

```bash
touch bin/load_ddl.sh
chmod +x bin/load_ddl.sh
```

### /bin/load_ddl.sh

```sh
#!/bin/bash

cat mysql/init.sql | docker-compose exec -T mysql mysql --user=root --password=rootpassword
```

init.sqlの出力結果をdocker-composeのmysqlに渡してSQLを実行

## スクリプトの実行

```bash
bin/load.sh
```
