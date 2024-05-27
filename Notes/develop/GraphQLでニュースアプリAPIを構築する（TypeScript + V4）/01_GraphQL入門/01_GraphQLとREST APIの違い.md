---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## APIの叩き方

以下のサンプルのように、必要な情報を取捨選択して一度のリクエストで済むように設計されているのがGraphQLとなる。一方REST APIは必要な情報の取得が別のAPIだったり不必要なものまで取得してしまうため効率が悪い。

### GraphQL

リクエスト

```json
query {
	User {id: xxxx} {
		name
		posts {
			title
		}
	}
}
```

レスポンス 

```json
{
	"data": {
		"User": {
			"name": "Mary",
			"posts": [
				{ title: "xxxxx" }
			]
		}
	}
}
```

### REST API 

リクエスト

```bash
curl GET /users/<id>
```

レスポンス

```json
{
	"user": {
		"id": "xxxxx",
		"name": "Many",
		"address": { ... },
		"birthday": "xxxxx"
	}
}
```

リクエスト

```bash
curl GET /users/<id>/posts
```

レスポンス

```json
{
	"posts": {
		"id": "xxxxx",
		"title": "xxxxx",
		"content": "xxxxx",
		"comments": [ ... ]
	}
}
```

## GraphQLのメリット

- 1つのエンドポイントで済む 
- 余計なデータを取得せずに済む
- 型指定でデータが明確になる