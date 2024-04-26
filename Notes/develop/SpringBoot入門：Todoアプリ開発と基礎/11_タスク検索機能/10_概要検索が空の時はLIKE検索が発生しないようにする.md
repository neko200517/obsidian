## Dynamic SQL

MyBatis の Dynamic SQL を利用して無駄な SQL を実行しないようにする

https://mybatis.org/mybatis-3/ja/dynamic-sql.html

## com/example/todo/repository/task/TaskRepository.java 

・SQL全体を script タグで囲う
・条件を指定したい where 句の場所に where タグで囲う（SQLのWHERE句は削除する）
・条件を指定したい場所に if タグを囲う
・summary が null または 空文字 の場合は WHERE 句を削除し、それ以外の場合は LIKE 検索を行う

```java
package com.example.todo.repository.task;

import java.util.List;
import java.util.Optional;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import com.example.todo.service.task.TaskEntity;
import com.example.todo.service.task.TaskSearchEntity;

@Mapper
public interface TaskRepository {

  @Select("""
        <script>
          SELECT id, summary, description, status
          FROM tasks
          <where>
            <if test='condition.summary != null and !condition.summary.isBlank()'>
              summary LIKE CONCAT('%', #{condition.summary}, '%')
            </if>
          </where>
        </script>
      """)
  List<TaskEntity> select(@Param("condition") TaskSearchEntity condition);

  @Select("SELECT id, summary, description, status FROM tasks WHERE id = #{taskId};")
  Optional<TaskEntity> selectById(@Param("taskId") long taskId);

  @Insert("""
      INSERT INTO tasks(
          summary,
          description,
          status
      )
      VALUES(
          #{task.summary},
          #{task.description},
          #{task.status}
      )
      ;
      """)
  void insert(@Param("task") TaskEntity newEntity);

  @Update("""
      UPDATE
          tasks
      SET
          summary = #{task.summary},
          description = #{task.description},
          status = #{task.status}
      WHERE
          id = #{task.id}
      ;
      """)
  void update(@Param("task") TaskEntity entity);

  @Delete("DELETE from tasks WHERE id = #{id};")
  void delete(@Param("id") long id);
}
```