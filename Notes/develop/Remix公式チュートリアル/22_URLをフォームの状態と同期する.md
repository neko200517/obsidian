---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - Remix
aliases:
  - <% tp.file.title %>
---
## app/root.tsx

- 検索後にページを更新するとリストがフィルタリングされているにも関わらずフォームフィールドに値が含まれなくなるので修正する
- jsonに q を追加して、受け取り側で取得できるようにする
- 検索後、defaultValueを設定して読み込み前の値を保持する

```tsx
// existing imports & exports

export const loader = async ({
  request,
}: LoaderFunctionArgs) => {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return json({ contacts, q });
};

export default function App() {
  const { contacts, q } = useLoaderData<typeof loader>();
  const navigation = useNavigation();

  return (
    <html lang="en">
      {/* existing elements */}
      <body>
        <div id="sidebar">
          {/* existing elements */}
          <div>
            <Form id="search-form" role="search">
              <input
                aria-label="Search contacts"
                defaultValue={q || ""}
                id="q"
                name="q"
                placeholder="Search"
                type="search"
              />
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

## 入力値を同期する

- 検索後に「戻る」をクリックするとリストはフィルタリングされなくなるが、フォームフィールドには入力した値が残るので修正する

```tsx
// existing imports
import { useEffect } from "react";

// existing imports & exports

export default function App() {
  const { contacts, q } = useLoaderData<typeof loader>();
  const navigation = useNavigation();

  useEffect(() => {
    const searchField = document.getElementById("q");
    if (searchField instanceof HTMLInputElement) {
      searchField.value = q || "";
    }
  }, [q]);

  // existing code
}
```

## useStateを使った場合

- 上記処理をuseStateで実装した例

```tsx
// existing imports
import { useEffect, useState } from "react";

// existing imports & exports

export default function App() {
  const { contacts, q } = useLoaderData<typeof loader>();
  const navigation = useNavigation();
  // the query now needs to be kept in state
  const [query, setQuery] = useState(q || "");

  // we still have a `useEffect` to synchronize the query
  // to the component state on back/forward button clicks
  useEffect(() => {
    setQuery(q || "");
  }, [q]);

  return (
    <html lang="en">
      {/* existing elements */}
      <body>
        <div id="sidebar">
          {/* existing elements */}
          <div>
            <Form id="search-form" role="search">
              <input
                aria-label="Search contacts"
                id="q"
                name="q"
                // synchronize user's input to component state
                onChange={(event) =>
                  setQuery(event.currentTarget.value)
                }
                placeholder="Search"
                type="search"
                // switched to `value` from `defaultValue`
                value={query}
              />
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