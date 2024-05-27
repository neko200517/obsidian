---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApolloServer
aliases:
  - <% tp.file.title %>
---

## クライアントで確認

以下のクエリを実行

```json
query ExampleQuery {
  feed {
    id
    description
    url
  }
}
```

以下のように帰ってきたらOK

```json
{
  "data": {
    "feed": [
      {
        "id": "link-0",
        "description": "GraphQLを学ぶ",
        "url": "https://google.com"
      }
    ]
  }
}
```