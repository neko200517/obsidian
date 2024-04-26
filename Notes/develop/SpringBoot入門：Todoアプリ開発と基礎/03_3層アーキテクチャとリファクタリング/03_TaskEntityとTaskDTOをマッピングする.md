## controller/task/TaskController.java

プレゼンテーション層で扱うTaskDTOに合わせるため、TaskEntityをTaskDTOに変換する

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import com.example.todo.service.task.TaskService;

@Controller
public class TaskController {

  private final TaskService taskService = new TaskService();

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
}
```

#### javaの記法として以下のように書き換えることができる

```java
map(entity -> TaskDTO.toDTO(entity))
↓
map(TaskDTO::toDTO)
```

## controller/task/taskDTO.java 

```java
package com.example.todo.controller.task;

import com.example.todo.service.task.TaskEntity;

public record TaskDTO(
    long id,
    String summary,
    String description,
    String status) {

  public static TaskDTO toDTO(TaskEntity entity) {
    return new TaskDTO(
        entity.id(),
        entity.summary(),
        entity.description(),
        entity.status().name());
  }
}
```