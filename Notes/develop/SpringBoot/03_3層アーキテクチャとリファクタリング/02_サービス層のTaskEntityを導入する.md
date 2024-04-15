## service/task/TaskEntity.java 

サービス層で扱うデータ型をサービス層で定義する

```java
// com.example.todo.service.task/TaskEntity.java

package com.example.todo.service.task;

public record TaskEntity(
    long id,
    String summary,
    String description,
    TaskStatus status) {
}
```

## service/task/TaskStatus.java

タスクの状態

```java
// com.example.todo.service.task/TaskStatus.java

package com.example.todo.service.task;

public enum TaskStatus {
  TODO,
  DOING,
  DONE
}
```

## service/task/TaskService.java

TaskDTO → TaskEntity に修正

```java
// com.example.todo.service.task/TaskService.java

package com.example.todo.service.task;

import java.util.List;

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