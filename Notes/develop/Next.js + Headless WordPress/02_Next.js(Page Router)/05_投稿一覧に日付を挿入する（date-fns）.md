## ドキュメント

### Getting-Started

https://date-fns.org/docs/Getting-Started

### フォーマット

https://date-fns.org/v3.6.0/docs/format

## ライブラリをインストール

```bash
npm i date-fns
```

## Dateコンポーネントの作成

- parseIsoで日付オブジェクトに変換
- formatで日付をフォーマットして文字列に変換
- timeタグ
	- dateTimeに日付文字列

### components/Date.tsx

```tsx
import { parseISO, format } from 'date-fns';

export default function Date({ dateString }: { dateString: string }) {
  const date = parseISO(dateString);

  return <time dateTime={dateString}>{format(date, 'LLLL d, yyyy')}</time>;
}
```

## 日付コンポーネントの挿入

- タイトルと本文の間に挿入
### pages/blog/index.tsx

```tsx
//...
                  <div className='py-4'>
                    Published on <Date dateString={post.date} />
                  </div>
//...
```