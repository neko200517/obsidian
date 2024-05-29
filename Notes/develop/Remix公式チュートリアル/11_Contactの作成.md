---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Remix
aliases:
  - <% tp.file.title %>
---
## app/root.tsx

- Newボタン（submitボタン）を押下するとaction関数が実行される
- 空のContactデータを作成する
- actionが実行された後、もう一度loaderが動作して画面更新される

```tsx
// existing imports

import { createEmptyContact, getContacts } from "./data";

export const action = async () => {
  const contact = await createEmptyContact();
  return json({ contact });
};

// existing code
```