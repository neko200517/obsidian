---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApolloServer
aliases:
  - <% tp.file.title %>
---
## 手順

1. リポジトリの最新化
2. node と npm をインストールする
3. n をインストールする
4. node の 最新 stable バージョンをインストールする
5. 最初に入れた node と npm をアンインストールする
6. bash を再起動する

```bash
sudo apt-get update
sudo apt install -y nodejs npm 
sudo npm install n -g
sudo n stable
sudo apt purge -y nodejs npm 
sudo apt autoremove -y 
node -v
npm -v
```
