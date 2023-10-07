## flex-grow

flexの子要素にflex-growを当てると要素を引き延ばしてくれる。

```tsx
// src/app/layout.tsx

// 中略

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang='ja'>
      <body className='container mx-auto bg-slate-700 text-slate-50'>
        <div className='flex flex-col min-h-screen'>
          <Header />
          <main className='flex-grow'>{children}</main>
          <Footer />
        </div>
      </body>
    </html>
  );
}

```

## h-screenを100dvhにする

tailwind の h-screen は 100vh なのでスマホ対応にするために 100dvh に拡張する。

### tailwind.config.tsx 

```ts
// tailwind.config.tsx 

import type { Config } from 'tailwindcss';

const config: Config = {
  // 中略
  theme: {
    extend: {
      // 中略
      height: {
        screen: '100dvh',
        'screen-small': '100svh',
        'screen-large': '100lvh',
      },
      minHeight: {
        screen: '100dvh',
        'screen-small': '100svh',
        'screen-large': '100lvh',
      },
      maxHeight: {
        screen: '100dvh',
        'screen-small': '100svh',
        'screen-large': '100lvh',
      },
    },
  },
  plugins: [],
};
export default config;
```