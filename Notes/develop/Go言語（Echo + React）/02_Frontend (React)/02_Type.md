## 型の定義

各種型を定義する。

```bash
mkdir src/types
touch src/types/index.ts
```

```ts
// src/types/index.ts 

export type Task = {
  id: number;
  title: string;
  created_at: Date;
  updated_at: Date;
};

export type CsrfToken = {
  csrf_token: string;
};

export type Credentials = {
  email: string;
  password: string;
};
```
