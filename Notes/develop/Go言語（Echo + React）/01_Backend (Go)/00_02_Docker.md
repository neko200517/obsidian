## ファイル構成

以下のファイル構成でDockerファイルを作成する。

```bash
.
├── app
├── build
│   ├── app
│   │   └── Dockerfile
│   └── db
│       ├── Dockerfile
│       └── init
│           └── init.sql
└── docker-compose.yml
```

## docker-compose.yml

以下のdocker-compose.ymlを作成しコンテナ同士を連携させる。
- app :
	- Go言語のコンテナ 
	- APIサービス
	- 8080ポートを開放
	- 永続化
- db
	- PostgreSQLのコンテナ 
	- Databaseサービス
	- 5432ポートを開放（外向きに5434を開放）
	- 永続化
	- 初期化

```yml
# ./docker-compose.yml

version: "3.8"
services:
# Go
  app:
    build:
      context: .
      dockerfile: ./build/app/Dockerfile
    volumes:
      - ./app:/usr/local/go/src/app
    tty: true
    depends_on:
      - db
    ports:
      - 8080:8080

# PostgreSQL
  db:
    build:
      context: .
      dockerfile: ./build/db/Dockerfile
    ports:
      - 5434:5432
    environment:
      POSTGRES_DB: test
      POSTGRES_USER: test
      POSTGRES_PASSWORD: test
      TZ: Asia/Tokyo
    restart: always
    volumes:
      - db-store:/var/lib/postgresql/data
      - ./build/db/init:/docker-entrypoint-initdb.d

volumes:
  db-store:
```

## build/app/Dockerfile 

Go言語のDockerfile

```Dockerfile
# build/app/Dockerfile

FROM golang:1.21.5-alpine

ENV ROOT=/usr/local/go/src/app

RUN apk update && apk add git

RUN mkdir -p ${ROOT}

WORKDIR ${ROOT}

COPY ./app ./

RUN go mod tidy

ENV GO_ENV=dev

EXPOSE 8080

CMD ["go", "run", "main.go"]
```

## build/db/Dockerfile 

PostgreSQLのDockerfile

```Dockerfile
# build/db/Dockerfile 

FROM postgres:15.1

RUN localedef -i ja_JP -c -f UTF-8 -A /usr/share/locale/locale.alias ja_JP.UTF-8
ENV LANG ja_JP.utf8
```

## build/db/init/init.sql

DBの初期化処理

```sql
/* build/db/init/init.sql */

CREATE DATABASE IF NOT EXISTS test;
```

## 起動

```bash
docker compose up -d --build
```

初回のみ（マイグレーション）

```bash
docker compose exec app sh
go run migrate/migrate.go
```
