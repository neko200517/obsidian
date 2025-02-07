## Undoの仕組み

https://tiptap.dev/docs/collaboration/documents/history
https://tiptap.dev/docs/editor/extensions/functionality/undo-redo#undo

## エディタの各イベント発生時にストアの状態を更新する

### Hooks一覧

https://tiptap.dev/docs/hocuspocus/server/hooks

onCreate(): エディタ作成時、エディタの参照をストアに設定
onDestory(): エディタ破棄時、エディタの参照をストアから削除
onUpdate(): エディタ更新時、エディタの参照をストアに設定
onSelectionUpdate(): エディタの参照をストアに設定
onTransaction(): エディタの参照をストアに設定
onFocus(): エディタの参照をストアに設定
onBlur(): エディタの参照をストアに設定
onContentError(): エディタの参照をストアに設定

### src/app/[documentId]/editor.tsx

```tsx
'use client';

import StarterKit from '@tiptap/starter-kit';
import TaskItem from '@tiptap/extension-task-item';
import TaskList from '@tiptap/extension-task-list';
import Table from '@tiptap/extension-table';
import TableCell from '@tiptap/extension-table-cell';
import TableHeader from '@tiptap/extension-table-header';
import TableRow from '@tiptap/extension-table-row';
import Image from '@tiptap/extension-image';
import ImageResize from 'tiptap-extension-resize-image';
import { useEditor, EditorContent } from '@tiptap/react';

import { useEditorStore } from '@/store/use-editor-store';

export const Editor = () => {
  const { setEditor } = useEditorStore();

  const editor = useEditor({
    onCreate({ editor }) {
      setEditor(editor);
    },
    onDestroy() {
      setEditor(null);
    },
    onUpdate({ editor }) {
      setEditor(editor);
    },
    onSelectionUpdate({ editor }) {
      setEditor(editor);
    },
    onTransaction({ editor }) {
      setEditor(editor);
    },
    onFocus({ editor }) {
      setEditor(editor);
    },
    onBlur({ editor }) {
      setEditor(editor);
    },
    onContentError({ editor }) {
      setEditor(editor);
    },
    editorProps: {
      attributes: {
        style: 'padding-left: 56px; padding-right: 56px',
        class:
          'focus:outline-none print:border-0 bg-white border border-[#C7C7C7] flex flex-col min-h-[1054px] w-[816px] pt-10 pr-14 pb-10 cursor-text',
      },
    },
    extensions: [
      StarterKit,
      TaskItem.configure({
        nested: true,
      }),
      TaskList,
      Table.configure({
        resizable: true,
      }),
      TableRow,
      TableHeader,
      TableCell,
      Image,
      ImageResize,
    ],
    content: `
        <table>
          <tbody>
            <tr>
              <th>Name</th>
              <th colspan="3">Description</th>
            </tr>
            <tr>
              <td>Cyndi Lauper</td>
              <td>Singer</td>
              <td>Songwriter</td>
              <td>Actress</td>
            </tr>
          </tbody>
        </table>
      `,
  });

  return (
    <div className='size-full overflow-x-auto bg-[#F9FBFD] px-4 print:p-0 print:bg-white print:overflow-visible'>
      <div className='min-w-max flex justify-center w-[816px] py-4 print:py-0 mx-auto print:w-full print:min-w-0'>
        <EditorContent editor={editor} />
      </div>
    </div>
  );
};
```

## ツールバー側でエディタの参照を取得し、Undoを操作する

### src/app/[documentId]/toolbar.tsx

```tsx
'use client';

import { cn } from '@/lib/utils';
import { LucideIcon, Redo2Icon, Undo2Icon } from 'lucide-react';

import { useEditorStore } from '@/store/use-editor-store';

interface ToolbarButtonProps {
  onClick?: () => void;
  isActive?: boolean;
  icon: LucideIcon;
}

const ToolbarButton = ({
  onClick,
  isActive,
  icon: Icon,
}: ToolbarButtonProps) => {
  return (
    <button
      onClick={onClick}
      className={cn(
        'text-sm h-7 min-w-7 flex items-center justify-center rounded-sm hover:bg-neutral-200/80',
        isActive && 'bg-neutral-200/80'
      )}
    >
      <Icon className='size-4' />
    </button>
  );
};

export const Toolbar = () => {
  const { editor } = useEditorStore();

  const sections: {
    label: string;
    icon: LucideIcon;
    onClick: () => void;
    isActive?: boolean;
  }[][] = [
    [
      {
        label: 'Undo',
        icon: Undo2Icon,
        onClick: () => editor?.chain().focus().undo().run(),
      },
      {
        label: 'Redo',
        icon: Redo2Icon,
        onClick: () => editor?.chain().focus().redo().run(),
      },
    ],
  ];

  return (
    <div className='bg-[#F1F4F9] px-2.5 py-0.5 rounded-[24px] min-h-[40px] flex items-center gap-x-0.5 overflow-x-auto'>
      {sections[0].map((item) => (
        <ToolbarButton key={item.label} {...item} />
      ))}
    </div>
  );
};
```