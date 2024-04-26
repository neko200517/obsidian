## com/example/todo/repository/task/TaskRepository.java 

・condition.status が Null または isEmpty で空のリストの場合以外の処理を追加する
・以下のようなSQLを動的に生成する
　例）SELECT id, summary, description, status FROM tasks WHERE status IN ( 'TODO' , 'DONE' );
・foreach で condition.status のリストを展開する
・separator で リストの要素の羅列にカンマを追加する
・MyBatis の機能で 前の条件がない状態でも AND を消してくれる

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
            <if test='condition.status != null and !condition.status.isEmpty()'>
              AND status IN (
                <foreach item='item' index='index' collection='condition.status' separator=','>
                  #{item}
                </foreach>
              )
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