## DTOクラスの定義

Recordクラスでコンストラクタの省略

```bash
touch com.example.todo.controller.task/TaskDTO.java
```

```java
// com.example.todo.controller.task/TaskDTO.java 

package com.example.todo.controller.task;

public record TaskDTO(
    long id,
    String summary,
    String description,
    String status) {
}
```

## TaskController.java 

model.addAttributeにDTOインスタンスを渡す

```java
// com.example.todo.controller.task/TaskController.java

package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class TaskController {
  @GetMapping("/tasks")
  public String list(Model model) {
    var task = new TaskDTO(
        1L,
        "Spring Boot を学ぶ",
        "TODO アプリケーションを作る",
        "TODO");
    model.addAttribute("task", task);
    return "tasks/list";
  }
}
```

## list.html

渡したインタンスのメンバを表示する

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
    <p th:text="${task.id}"></p>
    <p th:text="${task.summary}"></p>
    <p th:text="${task.description}"></p>
    <p th:text="${task.status}"></p>
  </body>
</html>
```