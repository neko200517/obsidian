## ルーティング

### Pages Router

- ファイルベース

### App Router 

- フォルダベース

## レイアウト

### Pages Router

- _\_app.js
- _\_document.js

### App Router 

- layout.js 
- page.js 

## データフェッチ

### Pages Router

- getStaticProps()
- getServerSideProps()

### App Router 

- どこでもfetch可能

## 静的サイト生成

### Pages Router

- getStaticPath()
	- fallbackがいる

### App Router 

- getnerateStaticParams()
	- fallback不要

## SEO

### Pages Router

- Head

### App Router 

- generateMetadata()

## API Routes

### Pages Router

- /pages/api
	- 例）/pages/api/form.js

### App Router 

- /app/api
	- フォルダ階層化でroute.jsを作成
		- 例）/api/form/route.js

## Revalidation & Caching

### Pages Router

- getServerSideProps()
	- 常に取得
	- returnのオプションにrevalication

### App Router 

- fetch 
	- cacheオプション
		- no-store : キャッシュを持たない。常に呼び出される
		- fetch(xxx, { cache: 'no-cache' })
	- nextオプション 
		- fetch(xxx, { next: { revalidation: 60 } })

## Server Components & Client Components 

### Pages Router

- コンポーネントは常にクライアント

### App Router 

- 基本的に全てのコンポーネントはサーバーコンポーネント
	- 'use client' ディレクティブを追加することでクライアントコンポーネントになる