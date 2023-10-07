## datetime型について

FastAPI の datetime型とJavaScript のDate型の互換性はないが、ISO型の文字列型に変換して送信することで FastAPI に値を渡すことができる。

### dayjs をインストールする

```bash
npm i -D dayjs
```

### dayjs をインポートして日本時間を有効にする

```tsx
import dayjs from 'dayjs';
import timezone from 'dayjs/plugin/timezone';
import utc from 'dayjs/plugin/utc';

dayjs.extend(timezone);
dayjs.extend(utc);
const jst = 'Asia/Tokyo';

// 中略
```

### Postする際にISO文字列に変換する

```tsx
// 中略
dayjs(startDateTime).tz(jst).toISOString(),
// 中略
```
