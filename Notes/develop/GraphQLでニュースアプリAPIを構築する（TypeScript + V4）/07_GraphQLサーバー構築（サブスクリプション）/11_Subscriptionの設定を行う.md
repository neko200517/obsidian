## src/resolvers/Subscription.ts

- 受信側のリゾルバを追加

```ts
//...
function newVoteSubscribe(parent: unknown, args: unknown, context: Context) {
  return context.pubsub.asyncIterator('NEW_VOTE');
}

export const newVote = {
  subscribe: newVoteSubscribe,
  resolve: (payload: Vote) => payload,
};
```

## src/schema.graphql

- SubscriptionにnewVoteを追加

```ts
//...
type Subscription {
  newLink: Link
  newVote: Vote
}
//...
```