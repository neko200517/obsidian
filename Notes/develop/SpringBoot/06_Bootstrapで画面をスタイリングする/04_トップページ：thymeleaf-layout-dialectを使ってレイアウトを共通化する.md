## thymeleaf-layout-dialectを導入する

```json
dependencies {
    implementation group: 'nz.net.ultraq.thymeleaf', name: 'thymeleaf-layout-dialect', version: '3.2.1'
}
```

## resources/templates/layout/layout.html

全ページ共通のテンプレートファイルを作成する

・html タグに xmlns:layout の追加
・埋め込む場所に section layout:fragment の追加

```html
<!DOCTYPE html>
<html
  lang="ja"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link
      rel="stylesheet"
      th:href="@{/webjars/bootstrap/5.2.3/css/bootstrap.min.css}"
    />
    <title>TODOアプリ</title>
  </head>
  <body>
    <script th:src="@{/webjars/bootstrap/5.2.3/js/bootstrap.min.js}"></script>
    <div class="container">
      <header>This is header</header>
      <section layout:fragment="content"></section>
      <footer>This is footer</footer>
    </div>
  </body>
</html>
```

## resources/templates/index.html 

共通部分以外の差分を作成する

・layout:decorate で読み込むテンプレートファイルを指定
・section layout:fragment の内側がテンプレートに差し込まれるコンテンツとなる

```html
<!DOCTYPE html>
<html
  lang="ja"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
  layout:decorate="~{layout/layout}"
>
  <head>
    <title>TODOアプリ</title>
  </head>
  <body>
    <section layout:fragment="content">
      <h1>TODOアプリ</h1>
      <a th:href="@{/tasks}">タスク一覧</a>
    </section>
  </body>
</html>
```