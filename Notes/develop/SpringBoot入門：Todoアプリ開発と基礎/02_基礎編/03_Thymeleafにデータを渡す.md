## タスク一覧に属性を追加

データの追加

model.addAttribute("属性名", "内容")

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class TaskController {
  @GetMapping("/tasks")
  public String list(Model model) {
    model.addAttribute("task", "Spring Bootを学ぶ");
    return "tasks/list";
  }
}
```

## list.htmlに属性を表示する

データの参照

th:text="${属性名}"

```html
<!DOCTYPE html>
<html lang="ja" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>タスク一覧</title>
  </head>
  <body>
    <h1>タスク一覧</h1>
    <a th:href="@{/}">トップページに戻る</a>
    <p th:text="${task}"></p>
  </body>
</html>
```
