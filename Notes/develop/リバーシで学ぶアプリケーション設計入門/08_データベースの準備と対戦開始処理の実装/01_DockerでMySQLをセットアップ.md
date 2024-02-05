## docker-compose.ymlを作成する

[02_02_UbuntuにDockerをインストールする]を参考にWSLにDockerをインストール。
立ち上がっていなかったらWSL2でDockerサービスを起動する。

```bash
sudo service docker start
```

```bash
mkdir reversi
touch reversi/docker-compose.yml
```

```yml
# docker-compose.yml

version: "3"
services:
  mysql:
    image: mysql:8.0.29
    platform: linux/x86_64
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: reversi
      MYSQL_USER: reversi
      MYSQL_PASSWORD: password
```

## コンテナを起動 

```bash
docker-compose up -d
```

## ログイン用のスクリプトを作成

```bash
mkdir bin
touch bin/connect_mysql.sh
chmod +x ./bin/connect_mysql.sh
```

### /bin/connect_mysql.sh

```sh
#!/bin/bash

docker-compose exec mysql mysql --user=reversi --password=password reversi
```

### スクリプトを起動

```bash
bin/connect_mysql.sh
```

mysqlにログインできたら成功

## コンテナの終了

```bash
docker-compose down
```