## Vercel 用に postinstall を追加

- postinstall を追加

### package.json

```ts
{
  "name": "trello-clone",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "postinstall": "prisma generate" // 追加
  },
  "dependencies": {
    "@clerk/nextjs": "^5.2.8",
    "@hello-pangea/dnd": "^16.6.0",
    "@prisma/client": "^5.17.0",
    "@radix-ui/react-accordion": "^1.2.0",
    "@radix-ui/react-avatar": "^1.1.0",
    "@radix-ui/react-dialog": "^1.1.1",
    "@radix-ui/react-label": "^2.1.0",
    "@radix-ui/react-popover": "^1.1.1",
    "@radix-ui/react-separator": "^1.1.0",
    "@radix-ui/react-slot": "^1.1.0",
    "@radix-ui/react-tooltip": "^1.1.2",
    "@tanstack/react-query": "^5.51.23",
    "class-variance-authority": "^0.7.0",
    "clsx": "^2.1.1",
    "date-fns": "^3.6.0",
    "lodash": "^4.17.21",
    "lucide-react": "^0.416.0",
    "next": "14.2.5",
    "next-themes": "^0.3.0",
    "react": "^18",
    "react-dom": "^18",
    "sonner": "^1.5.0",
    "stripe": "^16.8.0",
    "tailwind-merge": "^2.4.0",
    "tailwindcss-animate": "^1.0.7",
    "unsplash-js": "^7.0.19",
    "usehooks-ts": "^3.1.0",
    "zod": "^3.23.8",
    "zustand": "^4.5.4"
  },
  "devDependencies": {
    "@types/lodash": "^4.17.7",
    "@types/node": "^20",
    "@types/react": "^18",
    "@types/react-dom": "^18",
    "eslint": "^8",
    "eslint-config-next": "14.2.5",
    "postcss": "^8",
    "prisma": "^5.17.0",
    "tailwindcss": "^3.4.1",
    "typescript": "^5"
  }
}
```

## GitHub にソースコードをデプロイ

- GitHubにプライベートリポジトリを作成しておく

```bash
git add .
git commit -m "deployment"
git push origin master
```