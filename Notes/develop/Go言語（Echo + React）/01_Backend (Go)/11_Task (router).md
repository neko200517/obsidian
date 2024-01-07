## 実装

```go
// router/router.go 

package router

import (
	"go-echo/controller"
	"os"

	echojwt "github.com/labstack/echo-jwt/v4" // echojwtという別名を与える
	"github.com/labstack/echo/v4"
)

func NewRouter(uc controller.IUserController, tc controller.ITaskController) *echo.Echo {
	e := echo.New() // echoのインスタンスを作成

	// ユーザーAPI
	e.POST("/signup", uc.SignUp)
	e.POST("/login", uc.Login)
	e.POST("/logout", uc.Logout)

	// tasksグループを作成し、token認証していないとアクセスできないように設定
	t := e.Group("/tasks")
	t.Use(echojwt.WithConfig(echojwt.Config{
		SigningKey:  []byte(os.Getenv("SECRET")),
		TokenLookup: "cookie:token",
	}))

	// タスクAPI
	// :taskIdでURLパラメータの取得
	t.GET("", tc.GetAllTasks)
	t.GET("/:taskId", tc.GetTaskById)
	t.POST("", tc.CreateTask)
	t.PUT("/:taskId", tc.UpdateTask)
	t.DELETE("/:taskId", tc.DeleteTask)

	return e
}
```
