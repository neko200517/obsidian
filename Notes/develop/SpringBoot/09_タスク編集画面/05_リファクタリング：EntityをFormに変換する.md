## com/example/todo/controller/task/TaskForm.java 

・TaskEntity を TaskForm に変換する

```java
package com.example.todo.controller.task;

import com.example.todo.service.task.TaskEntity;
import com.example.todo.service.task.TaskStatus;

import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Pattern;
import jakarta.validation.constraints.Size;

public record TaskForm(
    @NotBlank @Size(max = 256, message = "256文字以内で入力してください") String summary,
    String description,
    @NotBlank @Pattern(regexp = "TODO|DOING|DONE", message = "Todo, Doing, Done のいずれかを選択してください") String status) {

  public TaskEntity toEntity() {
    return new TaskEntity(null, summary(), description(), TaskStatus.valueOf(status()));
  }

  public static TaskForm fromEntity(TaskEntity taskEntity) {
    return new TaskForm(taskEntity.summary(), taskEntity.description(), taskEntity.status().name());
  }
}
```

## com/example/todo/controller/task/TaskController.java 

・TaskService.findById で取得したデータは map で型に変換できる
・書き換え可能なラムダ式をメソッド参照にリファクタリングする
・shoDetail でも同様にリファクタリングする

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
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
    var taskDTO = taskService.findById(taskId)
        .map(TaskDTO::toDTO)
        .orElseThrow(TaskNotFoundException::new);
    model.addAttribute("task", taskDTO);
    return "tasks/detail";
  }

  @GetMapping("/creationForm")
  public String showCreationForm(@ModelAttribute TaskForm form) {
    return "tasks/form";
  }

  @PostMapping
  public String postCreationForm(@Validated TaskForm form, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
      return showCreationForm(form);
    }
    taskService.create(form.toEntity());
    return "redirect:/tasks";
  }

  @GetMapping("/{id}/editForm")
  public String showEditForm(@PathVariable("id") long taskId, Model model) {
    var form = taskService.findById(taskId)
        .map(TaskForm::fromEntity)
        .orElseThrow(TaskNotFoundException::new);
    model.addAttribute("taskForm", form);
    return "tasks/form";
  }
}
```