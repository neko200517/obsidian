## Wordpressプラグイン

- Yoast SEO
- Add WPGraphQL SEO 

## スキーマバリデータ

https://validator.schema.org/

## SEOクエリの作成

### lib/seo.ts

```ts
import { SeoPost } from '@/types/seo';
import graphqlRequest from './graphqlRequest';

export async function getSeo(
  pageType: string = 'post',
  slug: string = '/'
): Promise<SeoPost> {
  const query = {
    query: `query getSeo {
      ${pageType}(id: "${slug}", idType: SLUG) {
        seo {
          title
          metaDesc
          schema {
            raw
          }
          opengraphTitle
          opengraphDescription
          opengraphUrl      
          opengraphImage {
            mediaItemUrl
          }
          opengraphTitle
          opengraphSiteName
        }
      }
    }`,
  };

  const seoJson = await graphqlRequest(query);
  const seoData = seoJson.data[pageType].seo;

  return seoData;
}
```

## 型の定義

### types/seo.ts

```ts
export type SeoPost = {
  title: string;
  metaDesc: string;
  schema: {
    raw: string;
  };
  opengraphTitle: string;
  opengraphDescription: string;
  opengraphUrl: string;
  opengraphImage: {
    mediaItemUrl: string;
  } | null;
  opengraphType: string;
  opengraphSiteName: string;
};
```

## SeoHeadコンポーネントの作成

### components/SeoHead.tsx

```tsx
import { SeoPost } from '@/types/seo';
import { getImageUrl } from '@/utils/utils';
import Head from 'next/head';

export default function SeoHead({
  pathname,
  seoData,
  children,
}: {
  pathname: string;
  seoData: SeoPost;
  children: React.ReactNode;
}) {
  let ogImage = '';
  if (seoData.opengraphImage) {
    ogImage = getImageUrl(seoData.opengraphImage.mediaItemUrl);
  }

  let ogUrl = seoData.opengraphUrl.replaceAll(
    process.env.NEXT_PUBLIC_WP_BASE_URL!,
    `${process.env.NEXT_PUBLIC_FRONT_BASE_URL}${pathname}`
  );

  // WordpressのURLをフロントエンドに変換
  let jsonSchema = seoData.schema.raw.replaceAll(
    process.env.NEXT_PUBLIC_WP_BASE_URL!,
    `${process.env.NEXT_PUBLIC_FRONT_BASE_URL}${pathname}`
  );

  // ストレージの絶対パスでないURLを全て補完する
  jsonSchema = jsonSchema.replace(
    /\"\/wp-content\//g,
    `"${process.env.NEXT_PUBLIC_STORAGE_BASE_URL}/wp-content/`
  );

  // ストレージのURLに間違いがあれば補正
  jsonSchema = jsonSchema.replaceAll(
    `${process.env.NEXT_PUBLIC_FRONT_BASE_URL}${pathname}/wp-content/`,
    `${process.env.NEXT_PUBLIC_STORAGE_BASE_URL}/wp-content/`
  );

  return (
    <>
      <Head>
        <title key='title'>{seoData.title}</title>
        <meta name='description' content={seoData.metaDesc} key='metaDesc' />
        <meta property='og:title' content={seoData.opengraphTitle} />
        <meta
          property='og:description'
          content={seoData.opengraphDescription}
        />
        <meta property='og:url' content={ogUrl} />
        <meta property='og:image' content={ogImage} />
        <meta property='og:type' content={seoData.opengraphType} />
        <meta property='og:locale' content={seoData.opengraphType} />
        <meta property='og:site_name' content={seoData.opengraphSiteName} />

        <script
          type='application/ld+json'
          dangerouslySetInnerHTML={{ __html: jsonSchema }}
        ></script>
        {children}
      </Head>
    </>
  );
}
```

## 投稿ページに組み込み

### pages/blog/[postSlug].tsx

```tsx
export async function getStaticProps({
  params,
}: {
  params: { postSlug: string };
}) {
  //...
  const seoData = await getSeo('post', params.postSlug);

  return {
    props: {
      //...
      seoData,
    },
    //...
  };
}
//...

export default function Post({
  //...
  seoData,
}: {
  //...
  seoData: SeoPost;
}) {
  //...
  return (
    <>
      <SeoHead pathname='/blog' seoData={seoData}>
        //...
      </SeoHead>
    //...
```
