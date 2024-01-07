## Storeの作成

zustandで利用する状態管理用のカスタムフックを作成する。

```bash
mkdir src/store 
touch src/store/index.ts
```

```ts
// src/store/index.ts 

import { create } from 'zustand';

type EditedTask = {
  id: number;
  title: string;
};

type State = {
  editedTask: EditedTask;
  updateEditedTask: (payload: EditedTask) => void; // editedTask編集用
  resetEditedTask: () => void; // editedTaskの初期化
};

const useStore = create<State>((set) => ({
  editedTask: { id: 0, title: '' },
  updateEditedTask: (payload) => set({ editedTask: payload }),
  resetEditedTask: () => set({ editedTask: { id: 0, title: '' } }),
}));

export default useStore;
```
