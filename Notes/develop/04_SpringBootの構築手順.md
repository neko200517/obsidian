## Dockerの構築 

### ファイル構成

以下のようなファイル構成でプロジェクトフォルダを作成する。

```shell
.
├── docker-compose.yml
├── forDocker
│   └── db
│       └── initdb
└── server
    ├── Dockerfile
    └── src
```

### Serverに開発環境を構築する

VSCodeの拡張機能または spring initializr[https://start.spring.io/] でgradleプロジェクトを新規作成する。

例）
###### Project
- Project: Gradle - Groovy
- Language: Java 
- Spring Boot: 2.7.15
- Project Metadata:
	- Group: com.example
	- Artifact: demo
	- Name: demo 
	- Description: Demo project for Spring Boot 
	- Package name: com.example.demo
	- Packaging: Jar
	- Java: 11
###### Dependencies
- Sprint Web
- Lombok

作成したプロジェクトをserverフォルダに入れる。

```shell
./server
├── HELP.md
├── build.gradle
├── gradle
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
```

### docker-compose.ymlファイルの編集

開発用のDockerイメージを作成する。
appサーバにopenjdk11を、dbサーバにpostgreSQL13.1をインストールする。
###### ./docker-compose.yml
```yml
version: '3.6'
services:
  app:
    image: openjdk:11
    container_name: app
    ports:
      - 8080:8080
    tty: true
    volumes:
      - ./server:/srv:cached
    working_dir: /srv
    depends_on:
      - db
  db:
    image: postgres:13.1
    container_name: db
    environment:
      POSTGRES_USER: 'root'
      POSTGRES_PASSWORD: 'root'
      POSTGRES_DB: 'sample_db'
      TZ: 'Asia/Tokyo'
    ports:
      - '5432:5432'
    volumes:
      - dbvol:/var/lib/postgresql/data
      - ./forDocker/db/initdb:/docker-entrypoint-initdb.d
volumes:
  dbvol:
```

### initdbディレクトリに初期化用SQLを配置する

以下はusersテーブルを作成して初期データを投入し、ロールに権限を設定しているサンプルのSQLとなる。

###### ./forDocker/db/initdb/sample.sql
```sql
-- usresテーブルを作成
CREATE TABLE users (
	user_id character varying(16) NOT NULL
	-- 省略
);

-- 初期データを投入 
INSERT INTO users(
	user_id
	-- 省略
) VALUES (
	'user001'
	-- 省略
);

-- ロールを作成
CREATE ROLE sample_role WITH LOGIN PASSWORD 'password';

-- ロールに権限を設定
GRANT SELECT,UPDATE,INSERT,DELETE,TRUNCATE ON ALL TABLES IN SCHEMA public TO sample_role;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO sample_role;
```

### dockerのコンテナを立ち上げる

ここまで作成したらdockerイメージを作成し、コンテナを立ち上げてみる。

```shell
# appとdbコンテナを立ち上げる
docker-compose up -d
```

### コンテナの中に入ってみる

```shell
docker-compose exec app bash
```

### その他の操作

```shell
# コンテナを停止/削除
docker-compose down

# ボリュームを削除 (DBをリセットする場合)
docker volume rm dbvol # ボリューム名は環境によって異なる
```

### Spring BootのTomcatを立ち上げる 

中断はCtrl + C。コンテナから抜ける場合はexitを入力する。

```shell
# 初回のみビルド
sh gradlew build

# Tomcatサービスを立ち上げる 
java -jar build/libs/app-xxxx.jar # xxxにはバージョンが入る

# 中断 Ctrl + C

# コンテナから抜ける 
exit
```

### curlコマンドで外側からリクエストを投げる

テストする。

```shell
curl localhost:8080/api/hello
```

### DBに初期データが投入されているかを検証する

```shell
# dbコンテナに入る
docker-compose exec db bash

# PostgreSQLサーバにログイン
psql -U sample_role -d sample_db;

# 作成したテーブルとデータを検証
select * from users;

# ログアウト
exit
```

### デプロイ用のDockerfileを作成する

ここまで上手く構築と動作検証が終わったらデプロイ用にDockerfileを作成する。
###### ./server/Dockerfile
```Dockerfile
# from the base image of a jdk 11 container on Ubuntu 20.04.
FROM adoptopenjdk/openjdk11:x86_64-ubuntu-jdk-11.0.18_10-slim

# create a work dir.
WORKDIR /app

# copy a jvm app.
COPY build/libs/api-0.0.1-SNAPSHOT.jar app.jar

# open port 8080 for a jvm app.
EXPOSE 8080

# startup a jvm app.
ENTRYPOINT ["java","-jar","app.jar"]
```

### Dockerファイルをビルドする

```shell
# カレントディレクトリをDockerfileが存在する場所に移動
cd ./server

# キャッシュを含めずにイメージをビルド
docker build --no-cache .
```

## Azure Container Registory にデプロイする

事前に [[03_Azure Container Registoryを作成する]] でAzure上にコンテナリポジトリを作成しておく。

### Azureにデプロイするためにタグを変更

```shell
# 
```

