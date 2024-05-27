## src/resolvers/Mutation.ts

- postリゾルバを修正する
- publish関数でデータを送信する
	- 第一引数：トリガー名
	- 第二引数：送信するデータ

```ts
//...

// ニュースを投稿する
export async function post(
  parent: unknown,
  args: { url: string; description: string },
  context: Context
) {
  const { userId } = context;

  const newLink = await context.prisma.link.create({
    data: {
      description: args.description,
      url: args.url,
      postedBy: { connect: { id: userId } },
    },
  });

  // 送信
  context.pubsub.publish('NEW_LINK', newLink);

  return newLink;
}
```