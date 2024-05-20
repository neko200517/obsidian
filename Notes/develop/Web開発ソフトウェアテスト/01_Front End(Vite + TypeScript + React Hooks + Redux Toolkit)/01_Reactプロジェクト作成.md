---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - vite
  - typescript
  - react-hooks
  - redux-toolkit
  - test
aliases:
  - <% tp.file.title %>
---

## プロジェクト作成

Vite でプロジェクトを作成する

```bash
npm vite@latest
```

以下の質問に答える

```
ProjectName: default
Select a framework: React
Select a variant: TypeScript + SWC
```

## ライブラリのインストール

```bash
npm i axios
npm i react-router-dom @types/react-router-dom
npm i @mui/material @mui/icons-material @emotion/styled
npm i react-redux @reduxjs/toolkit
npm i -D msw
npm i -D vitest jsdom @testing-library/jest-dom @testing-library/react @testing-library/user-event @types/testing-library__user-event
```

## package.json

```json
{
  "name": "vite-project",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview",
    "test": "vitest",
    "coverage": "vitest run --coverage"
  },
  "dependencies": {
    "@emotion/styled": "^11.11.0",
    "@mui/icons-material": "^5.15.2",
    "@mui/material": "^5.15.2",
    "@reduxjs/toolkit": "^2.0.1",
    "@types/react-router-dom": "^5.3.3",
    "axios": "^1.6.2",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-redux": "^9.0.4",
    "react-router-dom": "^6.21.1"
  },
  "devDependencies": {
    "@testing-library/jest-dom": "^6.1.5",
    "@testing-library/react": "^14.1.2",
    "@testing-library/user-event": "^14.5.1",
    "@types/react": "^18.2.43",
    "@types/react-dom": "^18.2.17",
    "@types/testing-library__user-event": "^4.2.0",
    "@typescript-eslint/eslint-plugin": "^6.14.0",
    "@typescript-eslint/parser": "^6.14.0",
    "@vitejs/plugin-react-swc": "^3.5.0",
    "eslint": "^8.55.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.5",
    "jsdom": "^23.0.1",
    "msw": "^2.0.11",
    "typescript": "^5.2.2",
    "vite": "^5.0.8",
    "vitest": "^1.1.0"
  },
  "eslintConfig": {
    "extends": ["react-app", "react-app/jest"]
  }
}
```

## ローカルサーバの起動

```bash
npm run dev
```

## テスト

```bash
npm run test
```
