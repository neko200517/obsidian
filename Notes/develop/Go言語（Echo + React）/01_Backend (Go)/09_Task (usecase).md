## repositoryの作成

```bash
touch usecase/task_usecase.go
```

## 実装

```go
package usecase

import (
	"go-echo/model"
	"go-echo/repository"
)

// CRUD操作
type ITaskUsecase interface {
	GetAllTasks(userId uint) ([]model.TaskResponse, error)
	GetTaskById(userId uint, taskId uint) (model.TaskResponse, error)
	CreateTask(task model.Task) (model.TaskResponse, error)
	UpdateTask(task model.Task, userId uint, taskId uint) (model.TaskResponse, error)
	DeleteTask(userId uint, taskId uint) error
}

type taskUsecase struct {
	tr repository.ITaskRepository
}

// 依存性の抽入
// usecase → repository
func NewTaskUsecase(tr repository.ITaskRepository) ITaskUsecase {
	return &taskUsecase{tr}
}

// タスク一覧の取得
func (tu *taskUsecase) GetAllTasks(userId uint) ([]model.TaskResponse, error) {
	// タスク一覧の取得
	tasks := []model.Task{}
	if err := tu.tr.GetAllTasks(&tasks, userId); err != nil {
		return nil, err
	}

	// レスポンスモデルを生成
	resTasks := []model.TaskResponse{}
	for _, v := range tasks {
		t := model.TaskResponse{
			ID:        v.ID,
			Title:     v.Title,
			CreatedAt: v.CreatedAt,
			UpdatedAt: v.UpdatedAt,
		}
		resTasks = append(resTasks, t)
	}

	return resTasks, nil
}

// タスクの取得
func (tu *taskUsecase) GetTaskById(userId uint, taskId uint) (model.TaskResponse, error) {
	// タスクの取得
	task := model.Task{}
	if err := tu.tr.GetTaskById(&task, userId, taskId); err != nil {
		return model.TaskResponse{}, err
	}

	// レスポンスモデルを生成
	resTask := model.TaskResponse{
		ID:        task.ID,
		Title:     task.Title,
		CreatedAt: task.CreatedAt,
		UpdatedAt: task.UpdatedAt,
	}
	return resTask, nil
}

// タスクの作成
func (tu *taskUsecase) CreateTask(task model.Task) (model.TaskResponse, error) {
	// タスクの作成
	if err := tu.tr.CreateTask(&task); err != nil {
		return model.TaskResponse{}, err
	}

	// レスポンスモデルを生成
	resTask := model.TaskResponse{
		ID:        task.ID,
		Title:     task.Title,
		CreatedAt: task.CreatedAt,
		UpdatedAt: task.UpdatedAt,
	}

	return resTask, nil
}

// タスクの更新
func (tu *taskUsecase) UpdateTask(task model.Task, userId uint, taskId uint) (model.TaskResponse, error) {
	// タスクの更新
	if err := tu.tr.UpdateTask(&task, userId, taskId); err != nil {
		return model.TaskResponse{}, err
	}

	// レスポンスモデルを生成
	resTask := model.TaskResponse{
		ID:        task.ID,
		Title:     task.Title,
		CreatedAt: task.CreatedAt,
		UpdatedAt: task.UpdatedAt,
	}

	return resTask, nil
}

// タスクの削除
func (tu *taskUsecase) DeleteTask(userId uint, taskId uint) error {
	// タスクの削除
	if err := tu.tr.DeleteTask(userId, taskId); err != nil {
		return err
	}
	return nil
}
```
