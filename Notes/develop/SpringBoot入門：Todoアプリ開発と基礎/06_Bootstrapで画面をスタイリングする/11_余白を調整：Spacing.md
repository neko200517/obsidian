## resources/tempates/layout/layout.html

クラス名に margin, padding などをBootstrap の記法に従って指定する
https://getbootstrap.jp/docs/5.3/utilities/spacing/

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
    <title layout:title-pattern="$CONTENT_TITLE - $LAYOUT_TITLE">
      TODOアプリ
    </title>
  </head>
  <body>
    <script th:src="@{/webjars/bootstrap/5.2.3/js/bootstrap.min.js}"></script>

    <div class="container">
      <!-- Navbar -->
      <nav class="navbar navbar-expand-lg bg-body-tertiary">
        <div class="container-fluid">
          <a class="navbar-brand" th:href="@{/}">TODO</a>
          <button
            class="navbar-toggler"
            type="button"
            data-bs-toggle="collapse"
            data-bs-target="#navbarSupportedContent"
            aria-controls="navbarSupportedContent"
            aria-expanded="false"
            aria-label="Toggle navigation"
          >
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
              <li class="nav-item">
                <a class="nav-link active" aria-current="page" th:href="@{/}"
                  >Home</a
                >
              </li>
              <li class="nav-item">
                <a class="nav-link" th:href="@{/tasks}">タスク一覧</a>
              </li>
            </ul>
          </div>
        </div>
      </nav>
      <!-- Content -->
      <section layout:fragment="content" class="mt-3"></section>
    </div>
  </body>
</html>
```