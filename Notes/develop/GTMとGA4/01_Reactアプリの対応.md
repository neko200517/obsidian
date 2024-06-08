## 流れ

1. 必要に応じてGoogleアカウントの作成
2. GTMのアカウント作成
3. GA4のアカウント作成
4. タグの作成
5. Reactアプリの改修

## ライブラリ

```bash
npm i react-gtm-module
npm i -D @types/react-gtm-module
```

## タグの読込

- GTMのIDを登録する
- 自動でhtmlにスクリプトを埋め込んでくれる
- App.tsxなど、読み込み時に一度だけ走るところに書けばOK
- 毎ページ初期化する必要はない

### App.tsx

```tsx
useEffect(() => {
  TagManager.initialize({gtmId: "GTM-xxxxxxx"});
});
```

## タイトルの変更

- GA4 に登録するためにタイトルをページ単位に変更する
- document.title を変更すればよい
- React-Helmet などは不要

```tsx
useEffect(() => {
  document.title = '/'
}, [location]);
```

## 離脱クリックの対応

- GA4の拡張で離脱クリックの検知にチェックを入れる
- a タグでないと認識しない（window.openは認識できない）
- アプリ側では a タグに改修する必要がある
- a タグが使えない場合か後述カスタムイベントを発火する必要がある

## カスタムイベントの作成（GTM）

## カスタムイベントの作成 （React）

- メニューなど動的に作成される要素は a タグの認識がされないため、カスタムイベントを発火する

```tsx
onClick = {() => {
  TagManager.dataLayer({
    dataLayer: {
      event: 'click',
      link_url: '/'
      ...
    }
  });
}};
```