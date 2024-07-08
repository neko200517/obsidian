## Google Font 

https://fonts.google.com 

## インポート

```tsx
import { Rubik, Roboto_Slab } from 'next/font/google';

const rubik = Rubik({
  subsets: ['latin'],
  display: 'swap',
});

const roboto_slub = Roboto_Slab({
  subsets: ['latin'],
  display: 'swap',
});
```

## 基本的な使い方

```tsx
<article className={`${rubik.className}`}>
  //...
</article>
```

## 埋め込み

### APIから取得したコンテンツなど、直接クラス名を指定できない場合

```tsx
<Head>
	<title key={postData.slug}>{postData.title}</title>
	<meta
	  name='description'
	  content={postData.excerpt}
	  key='metadescription'
	/>
	<style>
	  {`
		  .post-content ul {
			font-family: ${roboto_slub.style.fontFamily}
		  }
		`}
	</style>
  </Head>
```