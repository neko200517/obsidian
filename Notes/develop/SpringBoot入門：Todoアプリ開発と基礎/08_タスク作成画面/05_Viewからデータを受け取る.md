## resources/templates/tasks/form.html 

name 属性を追加し、名前を割り当てる

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
        <form th:action="@{/tasks}" method="post">
          <div>
            <label for="summaryInput">概要</label>
            <input type="text" id="summaryInput" name="summary" />
          </div>
          <div>
            <label for="descriptionInput">詳細</label>
            <textarea id="descriptionInput" name="description"></textarea>
          </div>
          <div>
            <label for="statusInput">ステータス</label>
            <select id="statusInput" name="status">
              <option value="TODO">Todo</option>
              <option value="DOING">Doing</option>
              <option value="DONE">Done</option>
            </select>
          </div>
          <div>
            <button type="submit">作成</button>
          </div>
        </form>
      </div>
    </section>
  </body>
</html>
```

## com/example/todo/controller/task/TaskController.java 

Controller で name と同じ名前の変数を受け取るようになっている。
nameと同じメンバを持つクラスに割り当てることができる

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;

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

  @GetMapping("/tasks/creationForm")
  public String showCreationForm() {
    return "tasks/form";
  }

  @PostMapping("/tasks")
  public String postCreationForm(TaskForm form, Model model) {
    return list(model);
  }
}
```

## com/example/todo/controller/task/TaskForm.java 

String型で定義する

```java
package com.example.todo.controller.task;

public record TaskForm(
    String summary,
    String description,
    String status) {
}
```