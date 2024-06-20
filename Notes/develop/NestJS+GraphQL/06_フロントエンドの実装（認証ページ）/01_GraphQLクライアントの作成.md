## Apollo Client ドキュメント

https://www.apollographql.com/docs/react/api/link/introduction/

## フォルダ構成

```ts
 ├── src
    │   ├── apolloClient.ts
```

## Apollo Clientの作成

- ApolloClientにリンクを数珠つなぎに接続することで様々な機能を実装する
- httpリンク、authリンクを接続することでHeaderに認証情報を付与してhttpリクエストを行うクライアントを作成する
- InMemoryCacheでキャッシュ機能を利用できる

### src/apolloClient.ts

```ts
import { ApolloClient, createHttpLink, InMemoryCache } from '@apollo/client';
import { setContext } from '@apollo/client/link/context';

const httpLink = createHttpLink({
  uri: 'http://localhost:3000/graphql',
});

const authLink = setContext((_, prevContext) => {
  const token = localStorage.getItem('token');
  return {
    headers: {
      ...prevContext.headers,
      authorization: token ? `Bearer ${token}` : '',
    },
  };
});

const client = new ApolloClient({
  link: authLink.concat(httpLink),
  cache: new InMemoryCache(),
});

export default client;
```

## App.tsxにクライアントを組み込む

- ApolloProviderでApp.tsxのコンポーネントを囲む
- clientプロパティに作成したApolloClientを設定する

### src/App.tsx

```ts
//...
import client from './apolloClient';
import { ApolloProvider } from '@apollo/client';

function App() {
  return (
    <ApolloProvider client={client}>
      <BrowserRouter>
        //...
      </BrowserRouter>
    </ApolloProvider>
  );
}

export default App;
```