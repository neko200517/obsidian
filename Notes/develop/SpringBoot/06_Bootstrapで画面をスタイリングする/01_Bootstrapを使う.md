## Bootstrapのwebjarを導入する

```json
dependencies {
	implementation group: 'org.webjars', name: 'bootstrap', version: '5.2.3'
}
```

## resources/templates/index.html

stylesheet の読込、javascript の読込などを追加

```html
<!DOCTYPE html>
<html lang="ja" xmlns:th="http://www.thymeleaf.org">
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
    <h1>TODOアプリ</h1>
    <a th:href="@{/tasks}">タスク一覧</a>
  </body>
</html>
```