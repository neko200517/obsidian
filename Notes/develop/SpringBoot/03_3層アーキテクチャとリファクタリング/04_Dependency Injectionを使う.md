## service/task/TaskService.java 

@ServiceアノテーションでTaskServiceをBeanに登録

```java
package com.example.todo.service.task;

import java.util.List;

import org.springframework.stereotype.Service;

@Service
public class TaskService {
  public List<TaskEntity> find() {
    var task1 = new TaskEntity(
        1L,
        "Spring Boot を学ぶ",
        "TODO アプリケーションを作る",
        TaskStatus.TODO);

    var task2 = new TaskEntity(
        2L,
        "Spring Security を学ぶ",
        "ログイン機能を作ってみる",
        TaskStatus.DOING);

    return List.of(task1, task2);
  }
}
```

## controller/task/TaskController.java 

引数にTaskSerivceを登録してDIを実現する。
SpringFrameworkによりBeanに登録された同名のクラスが自動で注入される。

インジェクションしたいメンバにfinal修飾子を付与することで初期化が必須となり、コーディング漏れがあったとしてもNullPointerExceptionが発生しづらくなる。

```java
package com.example.todo.controller.task;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import com.example.todo.service.task.TaskService;

@Controller
public class TaskController {

  private final TaskService taskService;

  public TaskController(TaskService taskService) {
    this.taskService = taskService;
  }

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