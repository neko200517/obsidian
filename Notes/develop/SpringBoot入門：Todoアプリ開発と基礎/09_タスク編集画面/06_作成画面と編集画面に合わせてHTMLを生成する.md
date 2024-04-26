## com/example/todo/controller/task/TaskController.java 

・作成画面と編集画面に mode という属性を与える
・作成画面：CREATE 
・編集画面：EDIT

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
  public String showEditForm(@PathVariable("id") long taskId, Model model) {
    var form = taskService.findById(taskId)
        .map(TaskForm::fromEntity)
        .orElseThrow(TaskNotFoundException::new);
    model.addAttribute("taskForm", form);
    model.addAttribute("mode", "EDIT");
    return "tasks/form";
  }
}
```

## resource/templates/tasks/form.html

・mode によって処理を分ける

```html
<!DOCTYPE html>
<html
  lang="ja"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
  layout:decorate="~{layout/layout}"
>
  <head>
    <title th:text="${mode == 'CREATE'} ? 'タスク作成' : 'タスク編集'"></title>
  </head>
  <body>
    <section layout:fragment="content">
      <div>
        <form
          th:action="${mode == 'CREATE'} ? @{/tasks} : @{/tasks/{id}(id = ${id})}"
          th:method="${mode == 'CREATE'} ? post : put"
          th:object="${taskForm}"
        >
          <div class="form-group mt-3">
            <label for="summaryInput" class="form-label">概要</label>
            <input
              type="text"
              id="summaryInput"
              th:field="*{summary}"
              class="form-control"
              th:errorclass="is-invalid"
            />
            <span th:errors="*{summary}" class="invalid-feedback"></span>
          </div>
          <div class="form-group mt-3">
            <label for="descriptionInput" class="form-label">詳細</label>
            <textarea
              id="descriptionInput"
              th:field="*{description}"
              class="form-control"
              rows="10"
              th:errorclass="is-invalid"
            ></textarea>
            <span th:errors="*{description}" class="invalid-feedback"></span>
          </div>
          <div class="form-group mt-3">
            <label for="statusInput" class="form-label">ステータス</label>
            <select
              id="statusInput"
              th:field="*{status}"
              class="form-control"
              th:errorclass="is-invalid"
            >
              <option value="TODO">Todo</option>
              <option value="DOING">Doing</option>
              <option value="DONE">Done</option>
            </select>
            <span th:errors="*{status}" class="invalid-feedback"></span>
          </div>
          <div class="mt-3">
            <button
              type="submit"
              class="btn btn-primary"
              th:text="${mode == 'CREATE'} ? '作成' : '更新'"
            ></button>
            <a
              th:href="${mode == 'CREATE'} ?  @{/tasks} : @{/tasks/{id}(id=${id})}"
              class="btn btn-secondary"
              >戻る</a
            >
          </div>
        </form>
      </div>
    </section>
  </body>
</html>
```