---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Remix
aliases:
  - <% tp.file.title %>
---
## app/routers/contacts.$contactId.tsx

- http://localhost:5173/contacts/{contactId} のcontactIdを取得するにはparamsから値を取得する

```tsx
import { json } from "@remix-run/node";
import { Form, useLoaderData } from "@remix-run/react";
// existing imports

import { getContact } from "../data";

export const loader = async ({ params }) => {
  const contact = await getContact(params.contactId);
  return json({ contact });
};

export default function Contact() {
  const { contact } = useLoaderData<typeof loader>();

  // existing code
}

// existing code
```