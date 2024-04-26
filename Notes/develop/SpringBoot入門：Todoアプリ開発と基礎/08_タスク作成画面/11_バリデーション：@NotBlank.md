## build.gradle

バリデーションを実装するために以下のライブラリを導入する

```json
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-validation'
}
```

## com/example/todo/controller/task/TaskForm.java 

@NotBlank アノテーションをつけて値を必須項目にする

```java
package com.example.todo.controller.task;

import com.example.todo.service.task.TaskEntity;
import com.example.todo.service.task.TaskStatus;

import jakarta.validation.constraints.NotBlank;

public record TaskForm(
    @NotBlank String summary,
    String description,
    @NotBlank String status) {

  public TaskEntity toEntity() {
    return new TaskEntity(null, summary(), description(), TaskStatus.valueOf(status()));
  }
}
```

## com/example/todo/controller/task/TaskController.java

・バリデーションを行いたい引数に @Validated アノテーションを付与する
・処理結果が BindingResult に入っているので、hasErrors が True の場合にフォーム画面を再表示するように実装する

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import com.example.todo.service.task.TaskService;

import lombok.RequiredArgsConstructor;

@Controller
@RequiredArgsConstructor
@RequestMapping("/tasks")
public class TaskController {

  private final TaskService taskService;

  @GetMapping
  public String list(Model model) {
    // List<TaskEntity> -> List<TaskDTO>
    var taskList = taskService.find()
        .stream()
        .map(TaskDTO::toDTO)
        .toList();

    model.addAttribute("taskList", taskList);
    return "tasks/list";
  }

  @GetMapping("/{id}")
  public String showDetail(@PathVariable("id") long taskId, Model model) {
    // taskId -> TaskEntity
    var taskEntity = taskService.findById(taskId)
        .orElseThrow(() -> new IllegalArgumentException("Task not found: id = " + taskId));
    model.addAttribute("task", TaskDTO.toDTO(taskEntity));
    return "tasks/detail";
  }

  @GetMapping("/creationForm")
  public String showCreationForm() {
    return "tasks/form";
  }

  @PostMapping
  public String postCreationForm(@Validated TaskForm form, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
      return "tasks/form";
    }
    taskService.create(form.toEntity());
    return "redirect:/tasks";
  }
}
```