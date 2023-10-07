## 削除APIの作成

methodをDELETEにする。

```tsx
// 記事の削除
export const deleteArticle = async (id: string): Promise<Response> => {
  const res = await fetch(`${BASE_URL}/posts/${id}`, {
    method: 'DELETE',
  });

  if (!res.ok) {
    throw new Error(res.statusText);
  }

  return res;
};
```
