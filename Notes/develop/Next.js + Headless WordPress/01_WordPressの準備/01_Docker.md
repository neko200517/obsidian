## 参考URL

https://zenn.dev/fbd_tech/books/519201590c4e98/viewer/fbfe78

## フォルダ構成

```ts
.
├── front
│   // Next.jsのフォルダ
└── wp
    └── docker-compose.yml
```

## Dockerファイルの作成

### wp/docker-compose.yml

```yml
version: '3'

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - '8000:80'
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
volumes:
  db_data:
```

## コンテナの起動

```bash
docker-compose up -d
```