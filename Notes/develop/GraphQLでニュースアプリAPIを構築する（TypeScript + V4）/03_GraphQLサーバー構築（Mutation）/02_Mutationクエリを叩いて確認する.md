---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - GraphQL
  - ApploServer
aliases:
  - <% tp.file.title %>
---
## Mutation を実行

動作確認を行う。データが追加され、追加したデータが取得出来たらOK
### Operation

```json
mutation {
  post(url: "https://yahoo.co.jp", description: "test") {
    id
    description
    url
  }
}
```

### Response

```json
{
  "data": {
    "post": {
      "id": "link-1",
      "description": "test",
      "url": "https://yahoo.co.jp"
    }
  }
}
```

## Query を実行

動作確認を行う。追加されたデータがすべて表示されればOK

### Operation

```json
query ExampleQuery {
  feed {
    id
    description
    url
  }
}
```

### Response

```json
{
  "data": {
    "feed": [
      {
        "id": "link-0",
        "description": "GraphQLを学ぶ",
        "url": "https://google.com"
      },
      {
        "id": "link-1",
        "description": "test",
        "url": "https://yahoo.co.jp"
      }
    ]
  }
}
```