## 環境変数ファイル

.env.local を作成する。

```sh
touch .env.local
```

.env.localにSupabaseを使用するための情報を記述する。

```ts
// .env.local

NEXT_PUBLIC_SUPABSE_URL=https://zazuxvafmvugpqzgwpxh.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InphenV4dmFmbXZ1Z3Bxemd3cHhoIiwicm9sZSI6ImFub24iLCJpYXQiOjE2OTU5NTE3MTYsImV4cCI6MjAxMTUyNzcxNn0.TeHx-UJRUWBqnCmGZ8hM6UX5FxO2oYVzSZ82_zQt6Zg
```

Next.jsのAPIサーバーを呼び出すためのURLを追記する。

```ts
// .env.local

NEXT_PUBLIC_API_URL=http://localhost:3000
```

## ライブラリの導入

```sh
npm i -D @supabase/supabase-js
```

## supabaseClient.ts 

supabseClient.tsを作成して環境変数の読込とクライアントを作成する。

```ts
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = process.env.NEXT_PUBLIC_API_URL!;
const supabaseKey = process.env.NEXT_PUBLIC_ANON_KEY!;

export const supabase = createClient(supabaseUrl, supabaseKey, {
  auth: {
    persistSession: false,
  },
});
```
