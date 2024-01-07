## validator

### ファイルを作成

```bash
mkdir validator 
touch validator/task_validator.go
```

### 外部パッケージ

go-ozzo-validationを使用する

### task_validator.go

```go
// validator/task_validator.go 

package validator

import (
	"go-echo/model"

	validation "github.com/go-ozzo/ozzo-validation/v4"
)

type ITaskValidator interface {
	TaskValidate(task model.Task) error
}

type taskValidator struct{}

func NewTaskValidator() ITaskValidator {
	return &taskValidator{}
}

// バリデーションの実装
func (tv *taskValidator) TaskValidate(task model.Task) error {
	return validation.ValidateStruct(&task,
		validation.Field(
			&task.Title, // 対象フィールド
			validation.Required.Error("title is required"),            // 入力必須
			validation.RuneLength(1, 10).Error("limited max 10 char"), // 1～10文字以内
		),
	)
}
```

## usecaseに組み込み

### task_usecase.go 

#### taskStructに追加

```go
// usecase/task_usecase.go 

// 中略
type taskUsecase struct {
	tr repository.ITaskRepository
	tv validator.ITaskValidator // バリデーション
}
// 中略
```

#### NewTaskUsecaseに追加

```go
// usecase/task_usecase.go 

// 中略
func NewTaskUsecase(tr repository.ITaskRepository, tv validator.ITaskValidator) ITaskUsecase {
	return &taskUsecase{tr, tv}
}
// 中略
```

#### CreateTaskに追加

```go
// usecase/task_usecase.go 

// 中略
func (tu *taskUsecase) CreateTask(task model.Task) (model.TaskResponse, error) {
	// バリデーションチェック
	if err := tu.tv.TaskValidate(task); err != err {
		return model.TaskResponse{}, err
	}
	// 中略
}
```

#### UpdateTaskに追加 

```go
// usecase/task_usecase.go 

// 中略
func (tu *taskUsecase) UpdateTask(task model.Task, userId uint, taskId uint) (model.TaskResponse, error) {
	// バリデーションチェック
	if err := tu.tv.TaskValidate(task); err != nil {
		return model.TaskResponse{}, err
	}
	// 中略
}
```

## main.goに組み込み

```go
// main.go 

func main() {
    //　中略
    
    // バリデーション関係のインスタンスを生成
	taskValidator := validator.NewTaskValidator()

	// task関係のインスタンスを生成
	taskRepository := repository.NewTaskRepository(db)
	taskUsecase := usecase.NewTaskUsecase(taskRepository, taskValidator) // 追加
	taskController := controller.NewTaskController(taskUsecase)

    // 中略
}
```
