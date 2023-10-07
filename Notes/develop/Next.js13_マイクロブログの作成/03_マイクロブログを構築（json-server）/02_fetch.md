## API関数と型定義のファイルを作成

```sh
touch src/blogAPI.ts
touch src/types.ts
```

##  blogAPI.ts

```ts
import { Article } from './types';

export const getAllArticles = async (): Promise<Article[]> => {
  const res = await fetch('http://localhost:3100/posts', { cache: 'no-store' }); // SSR
  return await res.json();
};
```

## fetch関数のオプション

https://nextjs.org/docs/app/api-reference/functions/fetch

### SSG

データキャッシングあり。キャッシュが存在する限り再取得しない。

```ts
const res = await fetch('xxxx', { cache: 'force-cache' }); // 
```

### SSR 

データキャッシング無し。毎回取得する。

```ts
const res = await fetch('xxxx', { cache: 'no-store' }); // 
```

### ISR

10秒後に再取得

```ts
const res = await fetch('xxxx', {next: {revalidate: 10}}); // 
```
