## com/example/todo/controller/task/TaskController.java 

TaskEntity を TaskDTO に変換してビューに渡す

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
  public String showDetail(@PathVariable("id") long taskId, Model model) {
    // taskId -> TaskEntity
    var taskEntity = taskService.findById(taskId)
        .orElseThrow(() -> new IllegalArgumentException("Task not found: id = " + taskId));
    model.addAttribute("task", TaskDTO.toDTO(taskEntity));
    return "tasks/detail";
  }
}
```

## resources/tasks/detail.html

受け取ったtaskを表示する。
\+ や ' ' で文字列の連結が可能。

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
    <div th:object="${task}">
      <h2 th:text="'#' + *{id} + ' ' + *{summary}"></h2>
      <p th:text="*{status}"></p>
      <pre th:text="*{description}"></pre>
    </div>
  </body>
</html>
```