## webjars-locator-coreを導入する

```json
dependencies {
    implementation group: 'org.webjars', name: 'webjars-locator-core', version: '0.52'
}
```

## resources/templates/index.html

bootstrap の読み込み時にバージョン番号を指定しなくても読み込み可能になる

```html
<!DOCTYPE html>
<html lang="ja" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link
      rel="stylesheet"
      th:href="@{/webjars/bootstrap/css/bootstrap.min.css}"
    />
    <title>TODOアプリ</title>
  </head>
  <body>
    <script th:src="@{/webjars/bootstrap/js/bootstrap.min.js}"></script>
    <h1>TODOアプリ</h1>
    <a th:href="@{/tasks}">タスク一覧</a>
  </body>
</html>
```