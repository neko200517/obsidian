## app/layout/tsx

ルートレイアウトにglobal.cssを適用する。

```ts
import '@/app/ui/global.css';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

## Tailwind

プロジェクトにはtailwindがインストールされている。
試しに以下の要素を追加して動作を検証する（逆三角形のアイコンが表示される）。
Tailwind はスタイルシートの管理、スタイルの衝突、バンドルサイズの肥大化などを心配しなくて済むメリットがある。

```ts
<div
  className="h-0 w-0 border-b-[30px] border-l-[20px] border-r-[20px] border-b-black border-l-transparent border-r-transparent"
/>
```

## CSSモジュール

同様の結果をCSSモジュールで実装するには以下のように新しく xxx.module.css を追加してCSSを定義する。コンポーネント側で import しクラスを読み込むと三角形が表示される。
CSSモジュールのメリットはグローバルなcssに書くわけではなくコンポーネントごとに書くことになるため、スタイルの競合のリスクを軽減することができる。

### app/ui/home.module.css 

```css
.shape {
  height: 0;
  width: 0;
  border-bottom: 30px solid black;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```

```tsx
import styles from '@/app/ui/home.module.css';
<div className={styles.shape} />;
```

## clsx

条件によってスタイルを変更する場合などに使用するライブラリ。通常の jsx の記法だと参考演算子が良く使われるが、このライブラリを使用すると switch 分のように簡潔に書くことができる。

```tsx
import clsx from 'clsx';
 
export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

## その他

Sass, styled-jsx, emotion など