---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Vercel
  - react
  - Deploy
aliases:
  - <% tp.file.title %>
---
## React製のアプリをデプロイする際の注意

- ルート以外のURLを叩くと404エラーが出るため、対策として下記設定ファイルをプロジェクト直下に配置する

## vercel.json

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/" }]
}
```