## src/types.ts 

- 型を定義

```ts
export type Link = {
  id: number;
  description: string;
  url: string;
  postedBy: User;
};

export type User = {
  id: number;
  name: string;
  email: string;
  links: Link[];
};
```

## src/resolvers/Subscription.ts

- asyncIterator：非同期処理で繰り返す
- ’NEW_Link’：トリガー名
- newLinkのオブジェクトをリゾルバに設定する
	- subscribeに関数を実装する
	- resolve：後に使用するpublishの型を返す

```ts
import { Link } from '../types';
import { Context } from '../context';

function newLinkSubscribe(parent: unknown, args: unknown, context: Context) {
  return context.pubsub.asyncIterator('NEW_LINK');
}

export const newLink = {
  subscribe: newLinkSubscribe,
  resolve: (payload: Link) => payload,
};
```