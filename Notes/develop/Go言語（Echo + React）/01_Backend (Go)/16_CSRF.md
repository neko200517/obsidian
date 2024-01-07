## CSRF

クロスサイトフォージェリ対策としてCSRFのミドルウェアを追加する。
### router.go

・router.goにCSRF対策のミドルウェアを追加
・/csrfのエンドポイントを追加

```go
// router/router.go 

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

	// CSRF対策
	e.Use(middleware.CSRFWithConfig(middleware.CSRFConfig{
		CookiePath:     "/",                     // CSRFのcookieパス
		CookieDomain:   os.Getenv("API_DOMAIN"), // CSRFのcookieドメイン
		CookieHTTPOnly: true,                    // CSRF cookieがhttpオンリーかどうか
		CookieSameSite: http.SameSiteNoneMode,   // こちらはsecure modeがtrueになるためPostmanで動作確認する際はコメントアウトする
		// CookieSameSite: http.SameSiteDefaultMode, // こちらはPostmanで動作確認する時に使用する
		// CookieMaxAge:   60, // トークンの有効期限
	}))

	// ユーザーAPI
	e.POST("/signup", uc.SignUp)
	e.POST("/login", uc.Login)
	e.POST("/logout", uc.Logout)
	e.GET("/csrf", uc.CsrfToken) // 追加

	// 中略

}

```

### user_controller.go 

・user_controller.goにCsrfTokenメソッドを追加

```go
// controller/user_controller.go 

// user_controllerのインタフェース
type IUserController interface {
	SignUp(c echo.Context) error
	Login(c echo.Context) error
	Logout(c echo.Context) error
	CsrfToken(c echo.Context) error // 追加
}

// 中略 

// CSRFトークンを取得する
func (uc *userController) CsrfToken(c echo.Context) error {
	token := c.Get("csrf").(string)
	return c.JSON(http.StatusOK, echo.Map{
		"csrf_token": token,
	})
}
```

## 動作確認 

1. GET: http://localhost:8080/csrf にアクセス
2. Responceにcsrf_tokenを取得できるため値をコピーする
3. http://localhost:8080/login のHTTP HeadersにX-CSRF-TOKEN={csrf_token}を設定してアクセスする
4. エラーが返ってこなかったらOK

## 注意点

ローカルでテストするときは以下のようにしてSecureモードをfalseにしておく。

```go
CookieSameSite: http.SameSiteNoneMode,
// CookieSameSite: http.SameSiteDefaultMode,
```

サーバにデプロイした時には以下のように変更してSecureモードをtrueにしておく。

```go
// CookieSameSite: http.SameSiteNoneMode,
CookieSameSite: http.SameSiteDefaultMode,
```
