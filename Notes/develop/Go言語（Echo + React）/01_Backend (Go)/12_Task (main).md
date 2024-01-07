## 実装

```go
// main.go 

package main

import (
	"go-echo/controller"
	"go-echo/db"
	"go-echo/repository"
	"go-echo/router"
	"go-echo/usecase"
)

func main() {
	// dbインスタンスを作成
	db := db.NewDB()

	// 依存性の抽入（DI）
	// user
	userRepository := repository.NewUserRepository(db)          // db → repository
	userUsecase := usecase.NewUserUsecase(userRepository)       // repository → usecase
	userController := controller.NewUserController(userUsecase) // usecase → controller
	// task
	taskRepository := repository.NewTaskRepository(db)          // db → repository
	taskUsecase := usecase.NewTaskUsecase(taskRepository)       // repository → usecase
	taskController := controller.NewTaskController(taskUsecase) // usecase → controller

	// routerコンストラクタの起動
	e := router.NewRouter(userController, taskController)

	// サーバーの起動（+エラーのロギング、強制終了、ポート8080）
	e.Logger.Fatal(e.Start(":8080"))
}
```
