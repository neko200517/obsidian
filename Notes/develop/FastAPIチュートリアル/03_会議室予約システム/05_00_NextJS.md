## プロジェクトの作成

```sh
npx create-next-app@latest
```

質問されるが、すべてデフォルトで作成する

## 通信ライブラリのインストール

```sh
npm i -D axios
npm i -D swr
```

## 時間操作のライブラリ

```sh
npm i -D dayjs
```

## 文字列をhtmlに変換するライブラリ

```sh
npm i -D html-react-parser
```

## エラーバウンダリー

```sh
npm i -D react-error-boundary
```

## tailwindcssを設定順をソートしてくれるライブラリ 

```sh
npm i -D prettier-plugin-tailwindcss
```

プロジェクトのルートに .prettierrc を作成し、以下の設定で保存する

```json
{
  "plugins": ["prettier-plugin-tailwindcss"], // プラグインの有効化
  
  // 以下は保存時にシングルクォートの変換がうまく動作しないときに追記する
  "singleQuote": true,
  "jsxSingleQuote": true
}
```

## Suspense 

非同期APIを実行し、データ取得中のローディングを実現するためにSuspenseコンポーネントを使用している。
useSWR ( [[05_01_useSWR]] 参照 ) と組み合わせるには useSWR 利用時に ``` {suspense: true} ``` オプションを付与する。
ただしコンポーネント内の再描画が頻繁に行われる設計においては通信が頻繁に起こる可能性がある。その際はメモ化するなどして対策する必要あり。

```tsx
	// 省略
      <Suspense fallback={<div>Loading...</div>}>
        <UserList />
      </Suspense>
	//省略
```

## ErrorBoundary 

Suspenseコンポーネントと非同期処理の例外処理をフックするために必要。ErrorBoundary で囲んだ子コンポーネントで発生したすべての例外処理をフックして fallback で代替画面を表示することができる。

```tsx
  // 省略
  return (
    <ErrorBoundary fallback={<div>Error</div>}>
      <Suspense fallback={<div>Loading...</div>}>
        <UserList />
      </Suspense>
    </ErrorBoundary>
  );
```