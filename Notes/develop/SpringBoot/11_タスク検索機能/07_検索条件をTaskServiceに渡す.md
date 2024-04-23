## com/example/todo/service/task/TaskSearchEntity.java 

サービス層に渡すために Entity クラスを定義する

```java
package com.example.todo.service.task;

import java.util.List;

public record TaskSearchEntity(String summry, List<TaskStatus> status) {

}
```

## com/example/todo/service/task/TaskService.java 

find メソッドに TaskSearchEntity を受け取るようにする

```java
package com.example.todo.service.task;

import java.util.List;
import java.util.Optional;

import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.example.todo.repository.task.TaskRepository;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class TaskService {

  private final TaskRepository taskRepository;

  public List<TaskEntity> find(TaskSearchEntity searchEntity) {
    return this.taskRepository.select();
  }

  public Optional<TaskEntity> findById(long taskId) {
    return taskRepository.selectById(taskId);
  }

  @Transactional
  public void create(TaskEntity newEntity) {
    taskRepository.insert(newEntity);
  }

  @Transactional
  public void update(TaskEntity entity) {
    taskRepository.update(entity);
  }

  @Transactional
  public void delete(long id) {
    taskRepository.delete(id);
  }
}
```

## com/example/todo/controller/task/TaskController.java 

・searchForm.status リストを TaskStatus のリストに変換する
・searchForm.status はNullの可能性があるので Optional.Nullable で囲う
・Nullでない場合は TaskStatus のリストに変換する
・Nullの場合は List.of で空リストを作成する
・TaskSearchEntity に変換する
・TaskService.find メソッドに Entity を渡す

```java
package com.example.todo.controller.task;

import java.util.List;
import java.util.Optional;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import com.example.todo.service.task.TaskSearchEntity;
import com.example.todo.service.task.TaskService;
import com.example.todo.service.task.TaskStatus;

import lombok.RequiredArgsConstructor;

@Controller
@RequiredArgsConstructor
@RequestMapping("/tasks")
public class TaskController {

  private final TaskService taskService;

  @GetMapping
  public String list(TaskSearchForm searchForm, Model model) {
    var statusEntityList = Optional.ofNullable(searchForm.status()) // NULLの可能性があるリスト
        .map(statusList -> statusList.stream().map(TaskStatus::valueOf).toList()) // NULLでない場合
        .orElse(List.of()); // NULLの場合

    var searchEntity = new TaskSearchEntity(searchForm.summary(), statusEntityList);
    var taskList = taskService.find(searchEntity)
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
  public String showCreationForm(@ModelAttribute TaskForm form, Model model) {
    model.addAttribute("mode", "CREATE");
    return "tasks/form";
  }

  @PostMapping
  public String postCreationForm(@Validated TaskForm form, BindingResult bindingResult, Model model) {
    if (bindingResult.hasErrors()) {
      return showCreationForm(form, model);
    }
    taskService.create(form.toEntity());
    return "redirect:/tasks";
  }

  @GetMapping("/{id}/editForm")
  public String showEditForm(@PathVariable("id") long id, Model model) {
    var form = taskService.findById(id)
        .map(TaskForm::fromEntity)
        .orElseThrow(TaskNotFoundException::new);
    model.addAttribute("taskForm", form);
    model.addAttribute("mode", "EDIT");
    return "tasks/form";
  }

  @PutMapping("/{id}")
  public String update(
      @PathVariable("id") long id,
      @Validated @ModelAttribute TaskForm form,
      BindingResult bindingResult,
      Model model) {
    if (bindingResult.hasErrors()) {
      model.addAttribute("mode", "EDIT");
      return "tasks/form";
    }
    var entity = form.toEntity(id);
    taskService.update(entity);
    return "redirect:/tasks/{id}";
  }

  @DeleteMapping("/{id}")
  public String delete(@PathVariable("id") long id) {
    taskService.delete(id);
    return "redirect:/tasks";
  }
}
```