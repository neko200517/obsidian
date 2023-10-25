## Reactプロジェクトの作成

React + TypeScrptでプロジェクトを作成

```sh
npm create vite@latest
```

node_packageのインストール

```sh
cd project
npm i
```

## Storybookのインストール

```sh
npx storybook@latest init
```

## sanitize.cssのインストール 

CSSリセットであるsanitize.cssをインストール 

```sh
npm i -D sanitize.css
```

## sassのインストール 

scssコンパイラのsassをインストール

```sh
npm i -D sass
```

## .storybook/preview.tsにsanitize.cssをimport

```ts
// .storybook/preview.tsにsanitize.css

// 省略
import 'sanitize.css';
// 省略
```

## preview-head.htmlを作成してマテリアルアイコンを読み込み

```sh
touch .storybook/preview-head.html
```

```html
<link
  href="https://fonts.googleapis.com/icon?family=Material+Icons"
  rel="stylesheet"
/>
```

## stories/Atoms/Buttonディレクトリを作成し、最初のボタンを作成 

```sh
cd src/stories/
mkdir Atoms 
cd Atoms
mkdir Button
touch button.scss 
touch Button.stories.ts 
touch Button.tsx
```

## Button.stories.tsにメタ情報を宣言する

```ts
// Button.stories.ts

import type { Meta, StoryObj } from '@storybook/react';

import { Button } from './Button';

const meta = {
  title: 'Atoms/Button',
  component: Button,
  tags: ['autodocs'],
} satisfies Meta<typeof Button>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {
  args: {
    label: 'Button',
  },
};
```

## Button.tsx

```ts
// Button.tsx

import React from 'react';
import './button.scss';

interface ButtonProps {
  label: string;
}

export const Button = ({ label }: ButtonProps) => {
  return <button className='btn'>{label}</button>;
};
```

## button.scss

```scss
// button.scss

.btn {
  color: red;
  &:hover {
    color: blue;
  }
}
```

## Storrybookの起動

```sh
npm run storybook
```
