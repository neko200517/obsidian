## com/example/todo/controller/task/TaskForm.java 

TaskFrom を TaskEntity に変換する関数を作成する

```java
package com.example.todo.controller.task;

import com.example.todo.service.task.TaskEntity;
import com.example.todo.service.task.TaskStatus;

public record TaskForm(
    String summary,
    String description,
    String status) {

  public TaskEntity toEntity() {
    return new TaskEntity(null, summary(), description(), TaskStatus.valueOf(status()));
  }
}
```

## com/example/todo/controller/task/TaskController.java 

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
  public String postCreationForm(TaskForm form) {
    taskService.create(form.toEntity());
    return "redirect:/tasks";
  }
}
```