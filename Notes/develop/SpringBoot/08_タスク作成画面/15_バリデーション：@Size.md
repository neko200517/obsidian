## com/example/todo/controller/task/TaskFrom.java

・@Size アノテーションを付与して最大長の制約を追加する
・max で最大長の設定
・message でエラーメッセージの設定

```java
package com.example.todo.controller.task;

import com.example.todo.service.task.TaskEntity;
import com.example.todo.service.task.TaskStatus;

import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;

public record TaskForm(
    @NotBlank @Size(max = 256, message = "256文字以内で入力してください") String summary,
    String description,
    @NotBlank String status) {

  public TaskEntity toEntity() {
    return new TaskEntity(null, summary(), description(), TaskStatus.valueOf(status()));
  }
}
```