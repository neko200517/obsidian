## プロジェクトの作成

Vite + React + TypeScript + SWC でプロジェクトの作成

```bash
npm create vite@latest
Project name: ... react-todo
Select a framework: >> React 
Select a variant: >> TypeScript + SWC
```

## パッケージのインストール

- @tanstack/react-query ... データ取得・キャッシュライブラリ
- @tanstack/react-query-devtools ... react-queryの開発者ツール（任意）
- npm i zustand ... 状態管理
- npm i @heroicons/react  ... アイコン
- npm i react-router-dom ... ページ遷移
- npm i axios ... REST APIの実行
- npm i -D tailwindcss postcss autoprefixer ... スタイル

```bash
npm i @tanstack/react-query
npm i @tanstack/react-query-devtools
npm i zustand
npm i @heroicons/react 
npm i react-router-dom
npm i axios
npm i -D tailwindcss postcss autoprefixer
```

## .envファイルの作成 

```bash
touch .env
```

```ts
VITE_APP_API_URL=http://localhost:8080
```

import.meta.env.{VITE_xxxxx} で参照。

## スタイルの初期化

以下のコマンドでtailwindの初期化を行う。

```bash
npx tailwindcss init -p
```

実行後、tailwindcss.config.jsとpostcss.config.jsが作成されていることを確認する。

### tailwind.config.js

```js
// tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'], // ← ts, tsxを追加
  theme: {
    extend: {},
  },
  plugins: [],
};
```

### postcss.config.js

```js
// postcss.config.js

export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

次に、src/index.cssに以下のコードを追加する。

### src/index.css

```css
/* src/index.css */

@tailwind base;
@tailwind components;
@tailwind utilities;

/* 中略 */
```
