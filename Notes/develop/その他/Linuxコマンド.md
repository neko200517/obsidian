---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - tree
  - tips
  - Linux
aliases:
  - <% tp.file.title %>
---
## tree コマンド 

### 除外ファイル

プロジェクトの構成をtreeコマンドで書き題したい場合に不要なファイルやディレクトリを除外するには -I オプションを使用する。

```bash
tree -I "node_modules/"
```