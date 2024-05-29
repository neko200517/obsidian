---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Remix
aliases:
  - <% tp.file.title %>
---
## 検索フィールドに名前を入力してEnterキーを押す

- inputタグにname属性があればEnterを押下すると自動的に /?name=value が追加される

```
http://localhost:5173/?q=hoge
```

## app/root.tsx

- request.url で url を取得
- URLから q の値を取得し、前方一致するデータをフィルタリングする

```tsx
import type {
  LinksFunction,
  LoaderFunctionArgs,
} from "@remix-run/node";

// existing imports & exports

export const loader = async ({
  request,
}: LoaderFunctionArgs) => {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return json({ contacts });
};

// existing code
```