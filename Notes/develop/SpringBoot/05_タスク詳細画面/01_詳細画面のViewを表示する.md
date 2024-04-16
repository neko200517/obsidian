## resources/tasks/detail.html

タスク詳細画面のhtmlを作成

```html
<!DOCTYPE html>
<html lang="ja" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>タスク詳細</title>
  </head>
  <body>
    <h1>タスク詳細</h1>
    <a th:href="@{/tasks}">タスク一覧</a>
  </body>
</html>
```

## com/example/todo/controller/task/TaskController.java

Taskコントローラーに追加

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import com.example.todo.service.task.TaskService;

import lombok.RequiredArgsConstructor;

@Controller
@RequiredArgsConstructor
public class TaskController {

  private final TaskService taskService;

  @GetMapping("/tasks")
  public String list(Model model) {
    // List<TaskEntity> -> List<TaskDTO>
    var taskList = taskService.find()
        .stream()
        .map(TaskDTO::toDTO)
        .toList();

    model.addAttribute("taskList", taskList);
    return "tasks/list";
  }

  @GetMapping("/tasks/detail")
  public String showDetail() {
    return "tasks/detail";
  }
}
```