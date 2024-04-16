## com/example/todo/controller/task/TaskController.java

TaskService.findById というメソッドを呼び出す。
存在しない ID のデータを取得しようとしたら IlligalArgumentException を発生させる。

**Tips**
Optional → Nullを想定したクラス
orElseThrowを使用することでNullの場合処理を実行する

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
    model.addAttribute("taskId", taskEntity.id());
    return "tasks/detail";
  }
}
```

## com/example/todo/service/task/TaskService.java;

findById メソッドを作成する。
TaskRepository.selectById というメソッドを呼び出す。

```java
package com.example.todo.service.task;

import java.util.List;
import java.util.Optional;

import org.springframework.stereotype.Service;

import com.example.todo.repository.task.TaskRepository;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class TaskService {

  private final TaskRepository taskRepository;

  public List<TaskEntity> find() {
    return this.taskRepository.select();
  }

  public Optional<TaskEntity> findById(long taskId) {
    return taskRepository.selectById(taskId);
  }
}
```

## com/example/todo/repository/task/TaskRepository.java 

selectById というメソッドを作成して SQL を実装する。
@Param アノテーションを使用して SQL 内のパラメータに割り当てる。
SQL 内では #{taskId} という形で値を取得する。

```java
package com.example.todo.repository.task;

import java.util.List;
import java.util.Optional;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;

import com.example.todo.service.task.TaskEntity;

@Mapper
public interface TaskRepository {
  @Select("SELECT id, summary, description, status FROM tasks;")
  List<TaskEntity> select();

  @Select("SELECT id, summary, description, status FROM tasks WHERE id = #{taskId};")
  Optional<TaskEntity> selectById(@Param("taskId") long taskId);
}
```