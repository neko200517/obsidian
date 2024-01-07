## routerの作成

```bash
mkdir router 
touch router/router.go
```

```go
// router/router.go 

package router

import (
	"go-echo/controller"

	"github.com/labstack/echo/v4"
)

func NewRouter(uc controller.IUserController) *echo.Echo {
	e := echo.New() // echoのインスタンスを作成

	e.POST("/signup", uc.SignUp)
	e.POST("/login", uc.Login)
	e.POST("/logout", uc.Logout)

	return e
}
```
