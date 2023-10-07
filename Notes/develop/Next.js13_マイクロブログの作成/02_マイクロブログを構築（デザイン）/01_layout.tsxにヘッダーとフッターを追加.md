## layout.tsx

layout.tsx で前頁共通のレイアウトを定義することができる。

```tsx
// layout.tsx

// 中略
  return (
    <html lang='ja'>
      <body>
        <header>Header</header>
        {children}
        <footer>Footer</footer>
      </body>
    </html>
  );
// 中略
```