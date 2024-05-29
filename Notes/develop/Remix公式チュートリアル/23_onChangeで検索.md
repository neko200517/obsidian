---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Remix
aliases:
  - <% tp.file.title %>
---
## app/root.tsx

- useSubmit : onChangeをフックして変更があったらフォームを強制的に送信する
	- 送信するデータ : event.currentTarget

```tsx
// existing imports
import {
  Form,
  Links,
  Meta,
  NavLink,
  Outlet,
  Scripts,
  ScrollRestoration,
  useLoaderData,
  useNavigation,
  useSubmit,
} from "@remix-run/react";
// existing imports & exports

export default function App() {
  const { contacts, q } = useLoaderData<typeof loader>();
  const navigation = useNavigation();
  const submit = useSubmit();

  // existing code

  return (
    <html lang="en">
      {/* existing elements */}
      <body>
        <div id="sidebar">
          {/* existing elements */}
          <div>
            <Form
              id="search-form"
              onChange={(event) =>
                submit(event.currentTarget)
              }
              role="search"
            >
              {/* existing elements */}
            </Form>
            {/* existing elements */}
          </div>
          {/* existing elements */}
        </div>
        {/* existing elements */}
      </body>
    </html>
  );
}
```