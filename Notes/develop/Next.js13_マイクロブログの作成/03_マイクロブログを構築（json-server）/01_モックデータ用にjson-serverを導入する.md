## json-serverの導入

APIを手軽に試すことが可能なjson-serverを利用する。

```sh
npm i -D json-server
```

## サンプルjsonの用意

```sh
mkdir src/data
touch src/data/posts.json
```

```json
// posts.json 

{
  "posts": [
    {
      "id": "next.js13-udemy",
      "title": "Next.js13",
      "content": "Sample",
      "createdAt": "2023/09/22"
    },
    {
      "id": "json-server",
      "title": "json-server title",
      "content": "json-server content",
      "createdAt": "2023/09/22"
    }
  ]
}
```

## json-serverの起動

以下のコマンドを実行することで http://localhost:3001/posts にアクセス可能になる。

```sh
json-server watch src/data/posts.json --port 3001
```
