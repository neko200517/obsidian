## src/input.css 

繰り返し使用するスタイルは @apply を使用することで使いまわすことが可能になる

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

.btn {
  @apply font-bold py-2 px-4 rounded ml-10;
}

.btn-blue {
  @apply bg-blue-500 hover:bg-blue-600 text-white;
}

.btn-red {
  @apply bg-red-500 hover:bg-red-600 text-white;
}
```

## src/index.html

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="/dist/output.css" />
    <title>TailwindCSS入門</title>
  </head>
  <body>
    <div class="m-10">
      <button
        class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded"
      >
        ボタン
      </button>
      <button class="btn btn-blue">ボタン</button>
      <button class="btn btn-red">ボタン</button>
    </div>
  </body>
</html>
```