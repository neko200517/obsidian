## src/schema.graphql

- Mutationに vote 関数を追加
- Vote を追加
- Link に votes を追加

```ts
//...
type Mutation {
 //...
  vote(linkId: ID!): Vote
}
//...
type Vote {
  id: ID!
  link: Link!
  user: User!
}

type Link {
  //...
  votes: [Vote!]!
}
```