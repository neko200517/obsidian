## controllerの作成

```bash
mkdir controller 
touch controller/user_controller.go
```

## interfaceの作成

```go
// controller/user_controller.go 

package controller

import "github.com/labstack/echo"

type IUserController interface {
	SignUp(c echo.Context) error
	Login(c echo.Context) error
	Logout(c echo.Context) error
}
```

## interfaceの実装

サインアップ、ログイン、ログアウト

```go
package controller

import (
	"go-echo/model"
	"go-echo/usecase"
	"net/http"
	"os"
	"time"

	"github.com/labstack/echo/v4"
)

// user_controllerのインタフェース
type IUserController interface {
	SignUp(c echo.Context) error
	Login(c echo.Context) error
	Logout(c echo.Context) error
}

// user_controllerの構造体
type userController struct {
	uu usecase.IUserUsecase
}

// user_controllerの依存性の注入
func NewUserController(uu usecase.IUserUsecase) IUserController {
	return &userController{uu}
}

// サインアップ
// DBにユーザーを新規登録（Emailの重複不可）
func (uc *userController) SignUp(c echo.Context) error {
	// オブジェクトに変換
	// Bind：リクエストに含まれるjsonをuserモデルに適用する
	user := model.User{}
	if err := c.Bind(&user); err != nil {
		return c.JSON(http.StatusBadRequest, err.Error()) // 400
	}

	// DBにユーザーを作成
	userRes, err := uc.uu.SignUp(user)
	if err != nil {
		return c.JSON(http.StatusInternalServerError, err.Error()) // 500
	}

	// レスポンスを返す
	return c.JSON(http.StatusCreated, userRes) // 201, レスポンスにid, email
}

// ログイン
// cookieにトークンをセットする
func (uc *userController) Login(c echo.Context) error {
	user := model.User{}
	if err := c.Bind(&user); err != nil {
		return c.JSON(http.StatusBadRequest, err.Error())
	}

	// トークンを取得
	tokenString, err := uc.uu.Login(user)
	if err != nil {
		return c.JSON(http.StatusInternalServerError, err.Error()) // 500
	}

	// トークンをcookieに保存
	cookie := new(http.Cookie) // Cookie構造体のオブジェクトを作成
	cookie.Name = "token"
	cookie.Value = tokenString
	cookie.Expires = time.Now().Add(24 * time.Hour) // 有効期限
	cookie.Path = "/"
	cookie.Domain = os.Getenv("API_DOMAIN") // localhost
	cookie.Secure = true                    // ※ローカルテストするときにはコメントアウトする
	cookie.HttpOnly = true                  // javascriptで取得不可
	cookie.SameSite = http.SameSiteNoneMode // バックエンドとフロントエンドが異なるため
	c.SetCookie(cookie)                     // httpレスポンスにcookieをセット

	return c.NoContent(http.StatusOK) // 200
}

// ログアウト
// cookieをクリア
func (uc *userController) Logout(c echo.Context) error {
	// cookieをクリア
	cookie := new(http.Cookie)
	cookie.Name = "token"
	cookie.Value = ""
	cookie.Expires = time.Now()
	cookie.Path = "/"
	cookie.Domain = os.Getenv("API_DOMAIN")
	cookie.Secure = true // ※ローカルテストするときにはコメントアウトする
	cookie.HttpOnly = true
	cookie.SameSite = http.SameSiteNoneMode
	c.SetCookie(cookie)

	return c.NoContent(http.StatusOK)
}
```

## 注意点

ローカルでテストするときは以下のようにしてSecureモードをfalseにしておく。

```go
// cookie.Secure = true
```

サーバにデプロイした時には以下のように変更してSecureモードをtrueにしておく。

```go
cookie.Secure = true
```
