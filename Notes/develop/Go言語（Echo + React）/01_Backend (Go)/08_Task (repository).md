## repositoryの作成

```bash
touch repository/task_repository.go
```

## 実装

```go
package repository

import (
	"fmt"
	"go-echo/model"

	"gorm.io/gorm"
	"gorm.io/gorm/clause"
)

// CRUD操作一覧
type ITaskRepository interface {
	GetAllTasks(tasks *[]model.Task, userId uint) error           // タスク一覧の取得
	GetTaskById(task *model.Task, userId uint, taskId uint) error // タスクの取得
	CreateTask(task *model.Task) error                            // タスクの作成
	UpdateTask(task *model.Task, userId uint, taskId uint) error  // タスクの更新
	DeleteTask(userId uint, taskId uint) error                    // タスクの削除
}

type taskRepository struct {
	db *gorm.DB
}

// 依存性の抽入
// repository → gorm.DB
func NewTaskRepository(db *gorm.DB) ITaskRepository {
	return &taskRepository{db}
}

// タスク一覧の取得
func (tr *taskRepository) GetAllTasks(tasks *[]model.Task, userId uint) error {
	// Userテーブルと結合してuser_idをキーに一覧を取得、created_atで昇順に指定してtasksポインタに格納
	if err := tr.db.Joins("User").Where("user_id=?", userId).Order("created_at").Find(tasks).Error; err != nil {
		return err
	}
	return nil
}

// タスクの取得
func (tr *taskRepository) GetTaskById(task *model.Task, userId uint, taskId uint) error {
	// Userテーブルと結合してuser_idをキーに一覧を取得、主キーのtaskIdをキーに取得してtaskポインタに格納
	if err := tr.db.Joins("User").Where("user_id=?", userId).First(task, taskId).Error; err != nil {
		return err
	}
	return nil
}

// タスクの作成
func (tr *taskRepository) CreateTask(task *model.Task) error {
	// taskポインタの内容でDBに新規登録
	if err := tr.db.Create(task).Error; err != nil {
		return err
	}
	return nil
}

// タスクの更新
func (tr *taskRepository) UpdateTask(task *model.Task, userId uint, taskId uint) error {
	// tr.db.Model(task): モデルオブジェクトの指定
	// Clauses(clause.Returning{})：指定モデルに更新後の値を書き込み
	// Where("id=? AND user_id=?", taskId, userId)：SQLのWhere句のようにAND指定可能
	// Update("title", task.Title)：変更するフィールドを指定
	result := tr.db.Model(task).Clauses(clause.Returning{}).Where("id=? AND user_id=?", taskId, userId).Update("title", task.Title)
	if result.Error != nil {
		return result.Error
	}
	// 更新した行のカウントが1より少ない場合
	if result.RowsAffected < 1 {
		return fmt.Errorf("object does not exist")
	}
	return nil
}

// タスクの削除
func (tr *taskRepository) DeleteTask(userId uint, taskId uint) error {
	// Delete(&model.Task{})：削除対象のモデル構造体を指定
	result := tr.db.Where("id=? AND user_id=?", taskId, userId).Delete(&model.Task{})
	if result.Error != nil {
		return result.Error
	}
	if result.RowsAffected < 1 {
		return fmt.Errorf("object does not exist")
	}
	return nil
}
```
