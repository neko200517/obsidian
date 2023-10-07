## React.memo

メモ化していない場合：A と B が出力される。
メモ化している場合：a を変更すると A だけが表示され、b を変更すると B だけが表示される。
描画のみを行うような通常のコンポーネントにはメモ化は不要だが、再描画のたびにAPI通信を行っているようなコンポーネントの場合不要な通信が行われなくなるため有用となる。

```tsx
import React, { memo } from 'react'

const A = memo((props: { value: number }) => {
  console.log(`A: ${props.value}`);
  return <div>A</div>
});

const B = memo((props: { value: number }) => {
  console.log(`B: ${props.value}`);
  return <div>B</div>
});

export const App = () => {
  const [a, setA] = useState(0);
  const [b, setB] = useState(0);

  const onClickA = () => setA(x => x + 1);
  const onClickB = () => setB(x => x + 1);

  return (
    <main>
      <div>
        <button onClick={onClickA}>A</button>
        <A value={a} />
      </div>
      <div>
        <button onClick={onClickB}>B</button>
        <B value={b} />
      </div>
    </main>
  );
};
```

## useCallback

子コンポーネント側にコールバック関数を渡すときには memo だけではメモ化が効かなくなるため、コールバック関数に対して useCallback を使う。第二引数に変更を監視する状態の配列を入れる。

```tsx
import React, { memo, useCallback } from 'react'

export const A = memo((props: { value: number, onClick: () => void }) => {
  console.log(`A: ${props.value}`);
  return <button onClick={props.onClick}>A</button>
}

export const B = memo((props: { value: number, onClick: () => void }) => {
  console.log(`B: ${props.value}`);
  return <button onClick={props.onClick}>B</button>
}

export const App = () => {
  const [a, setA] = useState(0);
  const [b, setB] = useState(0);

  const onClickA = useCallback(() => setA(x => x + 1), [a]);
  const onClickB = useCallback(() => setB(x => x + 1), [b]);

  return (
    <main>
      <A value={a} onClick={onClickA} />
      <B value={b} onClick={onClickB} />
    </main>
  );
};
```

## useMemo

重い関数処理などに使う。

## forwardRef

親コンポーネント側から子コンポーネントの関数を叩きたい場合などに利用する。

```tsx
'use client';
import { forwardRef, useImperativeHandle, useRef } from 'react';

type Handler = {
  click: () => void;
};

const Child = forwardRef<Handler>((props, ref) => {
  const inputRef = useRef({} as HTMLInputElement);

  useImperativeHandle(ref, () => {
    return {
      click: () => {
        inputRef.current.focus();
      },
    };
  });

  return <input ref={inputRef} type='text' />;
});

export const Parent = () => {
  const ref = useRef({} as Handler);

  return (
    <main>
      <button onClick={() => ref.current.click()}>Click Me</button>
      <br />
      <Child ref={ref} />
    </main>
  );
};
```
