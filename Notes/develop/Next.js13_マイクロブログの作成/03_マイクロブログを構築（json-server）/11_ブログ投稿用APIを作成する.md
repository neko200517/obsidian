## POSTリクエスト

fetch 関数でPOSTリクエストを実行する。
実行すると src/data/posts.json に自動でデータを追加してくれる。

```tsx
// 中略
export const createArticle = async (
  article: PostArticle
): Promise<PostArticle> => {
  const currentDatetime = new Date().toISOString();

  const res = await fetch(`${BASE_URL}/posts`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ ...article, createdAt: currentDatetime }),
  });

  if (!res.ok) {
    throw new Error(res.statusText);
  }

  const newArticle = await res.json();
  return newArticle;
};
```