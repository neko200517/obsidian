## src/index.html

space-x-〇〇で余白を開ける

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="/dist/output.css" />
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap"
      rel="stylesheet"
    />
    <title>TailwindCSS入門</title>
  </head>
  <body class="font-poppins bg-body text-white">
    <!-- ナビゲーションバー -->
    <header class="py-6">
      <div
        class="container mx-auto flex justify-between items-center px-8 md:px-14 lg:px-24 w-full"
      >
        <div class="text-lg font-bold">JunTEC.com</div>
        <div class="space-x-12">
          <a href="#home">ホーム</a>
          <a href="#portfolio">ポートフォリオ</a>
          <a href="#clients">お客様</a>
          <a href="#contact"> お問い合わせ </a>
        </div>
      </div>
    </header>
  </body>
</html>

```
