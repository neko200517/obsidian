## resources/templates/error/404.html

error ディレクトリを作成し、ステータスコードに対応するファイルを作成すると対応したエラーが表示される。

```html
<!DOCTYPE html>
<html
  lang="ja"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
  layout:decorate="~{layout/layout}"
>
  <head>
    <title>404 Not Found</title>
  </head>
  <body>
    <section layout:fragment="content">
      <div class="text-center">
        <h1 class="display-1">404 Not Found</h1>
        <p>お探しのページは見つかりません</p>
        <a th:href="@{/}" class="btn btn-primary mt-3">トップページに戻る</a>
      </div>
    </section>
  </body>
</html>
```