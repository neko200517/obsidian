## CORS

フロントエンドからAPIを呼ぶためにはCORSを許可する必要がある。
router.goに以下のミドルウェアを追加する。

```go
// router/router.co

package router

import (
	"go-echo/controller"
	"net/http"
	"os"

	echojwt "github.com/labstack/echo-jwt/v4"
	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
)

func NewRouter(uc controller.IUserController, tc controller.ITaskController) *echo.Echo {

	// 中略

	// CORS対応
	// AllowOrigins: 許可するエンドポイント
	// AllowHeaders: Origin, Content-Type, Accept, Access-Control-Allow-Headers, X-CSRF-Token
	// AllowMethods: "GET", "PUT", "POST", "DELETE"
	// AlloCredentials: Cookieの送受信を可能にする
	e.Use(middleware.CORSWithConfig(middleware.CORSConfig{
		AllowOrigins: []string{"http://localhost:3000", os.Getenv("FE_URL")},
		AllowHeaders: []string{echo.HeaderOrigin, echo.HeaderContentType, echo.HeaderAccept,
			echo.HeaderAccessControlAllowHeaders, echo.HeaderXCSRFToken},
		AllowMethods:     []string{"GET", "PUT", "POST", "DELETE"},
		AllowCredentials: true,
	}))

	// 中略
	
}
```
