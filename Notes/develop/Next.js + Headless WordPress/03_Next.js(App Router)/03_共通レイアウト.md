## Tailwindの設定ファイル

### tailwind.config.ts

```ts
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      fontFamily: {
        rubik: ['var(--font-rubik)'],
        robotoSlab: ['var(--font-robotoSlub)'],
        notoSansJp: ['var(--font-notoSansJp)'],
      },
    },
  },
  plugins: [],
};
export default config;
```

## フォントファイル

### utils/fonts.ts

```ts
import { Roboto_Slab, Rubik, Noto_Sans_JP } from 'next/font/google';

export const rubik = Rubik({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-rubik',
});

export const robotoSlub = Roboto_Slab({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-robotoSlub',
});

export const notoSansJp = Noto_Sans_JP({
  subsets: ['latin'],
  weight: ['400'],
  display: 'swap',
  variable: '--font-notoSansJp',
});
```

## 共通レイアウト 

### app/layout.tsx

```tsx
import SiteFooter from '@/components/SiteFooter';
import { AppConfig } from '@/config/AppConfig';
import { notoSansJp, robotoSlub, rubik } from '@/utils/fonts';
import '@/styles/globals.css';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang='ja'>
      <head>
        {AppConfig.NO_INDEX && <meta name='robots' content='noindex' />}
      </head>
      <body
        className={`${rubik.variable} ${robotoSlub.variable} ${notoSansJp.variable}`}
      >
        <main className='min-h-[95dvh]'>{children}</main>
        <SiteFooter />
      </body>
    </html>
  );
}
```

## 共通CSS

### styles/globals.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
  .post-content p {
    @apply pb-4 text-lg;
  }
  .post-content a {
    @apply underline text-blue-400 hover:text-blue-600;
  }
  .post-content ul {
    @apply pb-4 list-disc list-inside text-yellow-600 text-xl;
  }
  .post-content figure {
    @apply pb-4;
  }
  .post-content figure figcaption {
    @apply text-center py-2;
  }
  .post-content h1 {
    @apply text-3xl font-bold py-4;
  }
  .post-content h2 {
    @apply text-2xl font-bold py-4;
  }
  .post-content h3 {
    @apply text-xl font-bold py-4;
  }
  .contact-form label,
  .memment-form label {
    @apply block w-full mb-4;
  }
  .contact-form input,
  .contact-form textarea,
  .comment-form input,
  .comment-form textarea {
    @apply block border focus:outline-none focus:ring focus:ring-yellow-300
    focus:ring-offset-2 focus:ring-opacity-50 w-full mb-4 p-2 rounded;
  }
  .contact-form button,
  .comment-form button {
    @apply inline-block bg-green-500 focus:outline-none focus:ring focus:ring-yellow-300
    focus:ring-offset-2 focus:ring-opacity-50 mb-4 p-3 text-slate-100 text-xl
    hover:bg-green-600 active:bg-green-700 rounded;
  }

  body {
    @apply font-rubik;
  }
  h1,
  h2,
  h3,
  h4,
  h5,
  h6 {
    @apply font-robotoSlab;
  }
  .post-content ul {
    @apply font-robotoSlab;
  }
}
```