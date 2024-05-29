---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Remix
aliases:
  - <% tp.file.title %>
---
## app/routes/contacts.$contactId_edit.tsx

- Editボタンを押下するとactionが実行される
- ActionFunctionArgs でURL パラメータとフォームデータを受け取ることができる
- Object.fromEntries ですべてのフォームデータのキーと値を取得してデータを更新する
- 処理が終わったらredirectで特定のURLにリダイレクトする

```tsx
import type {
  ActionFunctionArgs,
  LoaderFunctionArgs,
} from "@remix-run/node";
import { json, redirect } from "@remix-run/node";
// existing imports

import { getContact, updateContact } from "../data";

export const action = async ({
  params,
  request,
}: ActionFunctionArgs) => {
  invariant(params.contactId, "Missing contactId param");
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
};

// existing code
```
