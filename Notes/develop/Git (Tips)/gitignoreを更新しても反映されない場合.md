---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Git
  - tips
aliases:
  - <% tp.file.title %>
---
## 手順

1. .gitignore 編集
2. キャッシュを削除
3. commit & push

```bash
git rm -r --cached . //ファイル全体キャッシュ削除
git rm -r --cached [ファイル名] //ファイル指定してキャッシュ削除
```