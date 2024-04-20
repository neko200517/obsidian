## com/example/todo/controller/task/TaskController.java

@PathVariable を使用してMapping の {id} を取得する。
taskId を Model にバインドする。

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

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

  @GetMapping("/tasks/{id}")
  public String showDetail(@PathVariable("id") Long taskId, Model model) {
    model.addAttribute("taskId", taskId);
    return "tasks/detail";
  }
}
```

## resources/templates/tasks/detail.html

taskIdを表示する

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
    <p th:text="${taskId}"></p>
  </body>
</html>
```