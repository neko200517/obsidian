## repositoryの作成

```bash
touch controller/task_controller.go
```

## 実装

```go
package controller

import (
	"go-echo/model"
	"go-echo/usecase"
	"net/http"
	"strconv"

	"github.com/golang-jwt/jwt/v4"
	"github.com/labstack/echo/v4"
)

type ITaskController interface {
	GetAllTasks(c echo.Context) error
	GetTaskById(c echo.Context) error
	CreateTask(c echo.Context) error
	UpdateTask(c echo.Context) error
	DeleteTask(c echo.Context) error
}

type taskController struct {
	tu usecase.ITaskUsecase
}

// controller → usecase
func NewTaskController(tu usecase.ITaskUsecase) ITaskController {
	return &taskController{tu}
}

// タスク一覧を取得
func (tc *taskController) GetAllTasks(c echo.Context) error {
	// user_idをjwtトークンから取得
	user := c.Get("user").(*jwt.Token)
	claims := user.Claims.(jwt.MapClaims)
	userId := claims["user_id"]

	// タスク一覧を取得
	// any型のuserIdをfloat64に変換→uintに変換
	tasksRes, err := tc.tu.GetAllTasks(uint(userId.(float64)))

	if err != nil {
		return c.JSON(http.StatusInternalServerError, err.Error()) // 500
	}
	return c.JSON(http.StatusOK, tasksRes) // 200, タスク一覧
}

// タスクを取得
func (tc *taskController) GetTaskById(c echo.Context) error {
	// user_idをjwtトークンから取得
	user := c.Get("user").(*jwt.Token)
	claims := user.Claims.(jwt.MapClaims)
	userId := claims["user_id"]

	// taskIdの取得
	id := c.Param("taskId")       // URLパラメータからtaskIdを取得
	taskId, _ := strconv.Atoi(id) // string型をint型に変換

	// タスクを取得
	taskRes, err := tc.tu.GetTaskById(uint(userId.(float64)), uint(taskId))

	if err != nil {
		return c.JSON(http.StatusInternalServerError, err.Error()) // 500
	}
	return c.JSON(http.StatusOK, taskRes) // 200, タスク
}

// タスクを作成
func (tc *taskController) CreateTask(c echo.Context) error {
	// user_idをjwtトークンから取得
	user := c.Get("user").(*jwt.Token)
	claims := user.Claims.(jwt.MapClaims)
	userId := claims["user_id"]

	// リクエストBodyに含まれるjsonをタスクオブジェクトにバインド
	task := model.Task{}
	if err := c.Bind(&task); err != nil {
		return c.JSON(http.StatusBadRequest, err.Error()) // 400
	}
	// タスクオブジェクトのuserIdを現在のユーザーIDに変更
	task.UserId = uint(userId.(float64))

	// タスクを作成
	taskRes, err := tc.tu.CreateTask(task)

	if err != nil {
		return c.JSON(http.StatusInternalServerError, err.Error()) // 500
	}
	return c.JSON(http.StatusOK, taskRes) // 200, タスク
}

// タスクを更新
func (tc *taskController) UpdateTask(c echo.Context) error {
	// user_idをjwtトークンから取得
	user := c.Get("user").(*jwt.Token)
	claims := user.Claims.(jwt.MapClaims)
	userId := claims["user_id"]

	// taskIdの取得
	id := c.Param("taskId")       // URLパラメータからtaskIdを取得
	taskId, _ := strconv.Atoi(id) // string型をint型に変換

	// リクエストBodyに含まれるjsonをタスクオブジェクトにバインド
	task := model.Task{}
	if err := c.Bind(&task); err != nil {
		return c.JSON(http.StatusBadRequest, err.Error()) // 400
	}

	// タスクを更新
	taskRes, err := tc.tu.UpdateTask(task, uint(userId.(float64)), uint(taskId))

	if err != nil {
		return c.JSON(http.StatusInternalServerError, err.Error())
	}
	return c.JSON(http.StatusOK, taskRes)
}

// タスクを削除
func (tc *taskController) DeleteTask(c echo.Context) error {
	// user_idをjwtトークンから取得
	user := c.Get("user").(*jwt.Token)
	claims := user.Claims.(jwt.MapClaims)
	userId := claims["user_id"]

	// taskIdの取得
	id := c.Param("taskId")       // URLパラメータからtaskIdを取得
	taskId, _ := strconv.Atoi(id) // string型をint型に変換

	// タスクを削除
	err := tc.tu.DeleteTask(uint(userId.(float64)), uint(taskId))

	if err != nil {
		return c.JSON(http.StatusInternalServerError, err.Error())
	}
	return c.NoContent(http.StatusNoContent) // 204
}
```

## 注意点

jwtのバージョンを指定しないとエラーになるためgo.modを以下のように修正する

```go
// go.mod

// 中略
require (
	// 中略
	github.com/labstack/echo-jwt/v4 v4.1.0
	// 中略
)
// 中略
```

修正後、以下のコマンドでパッケージをインストールする

```bash
go mod tidy
```
