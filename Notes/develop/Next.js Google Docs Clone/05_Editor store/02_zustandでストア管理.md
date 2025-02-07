## 構成

```ts
.
└── store
    └── use-editor-store.ts
```

## zustandのインストール

```bash
npm i zustand@5.0.1 --legacy-peer-deps
```

## ストア関数の作成

setEditorでeditorを設定
editorでeditorを参照
使う側はuseEditorStoreを使用する

### src/store/use-editor-store.ts

```ts
import { create } from 'zustand';
import { type Editor } from '@tiptap/react';

interface EditorState {
  editor: Editor | null;
  setEditor: (editor: Editor | null) => void;
}

export const useEditorStore = create<EditorState>((set) => ({
  editor: null,
  setEditor: (editor) => set({ editor }),
}));
```