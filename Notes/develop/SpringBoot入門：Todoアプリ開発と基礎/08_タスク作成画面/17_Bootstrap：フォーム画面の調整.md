## resource/templates/tasks/form.html 

スタイリングを調整する

```html
<!DOCTYPE html>
<html
  lang="ja"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
  layout:decorate="~{layout/layout}"
>
  <head>
    <title>タスク作成</title>
  </head>
  <body>
    <section layout:fragment="content">
      <div>
        <form th:action="@{/tasks}" method="post" th:object="${taskForm}">
          <div class="form-group mt-3">
            <label for="summaryInput" class="form-label">概要</label>
            <input
              type="text"
              id="summaryInput"
              th:field="*{summary}"
              class="form-control"
            />
            <span th:errors="*{summary}"></span>
          </div>
          <div class="form-group mt-3">
            <label for="descriptionInput" class="form-label">詳細</label>
            <textarea
              id="descriptionInput"
              th:field="*{description}"
              class="form-control"
              rows="10"
            ></textarea>
            <span th:errors="*{description}"></span>
          </div>
          <div class="form-group mt-3">
            <label for="statusInput" class="form-label">ステータス</label>
            <select id="statusInput" th:field="*{status}" class="form-control">
              <option value="TODO">Todo</option>
              <option value="DOING">Doing</option>
              <option value="DONE">Done</option>
            </select>
            <span th:errors="*{status}"></span>
          </div>
          <div class="mt-3">
            <button type="submit" class="btn btn-primary">作成</button>
          </div>
        </form>
      </div>
    </section>
  </body>
</html>
```