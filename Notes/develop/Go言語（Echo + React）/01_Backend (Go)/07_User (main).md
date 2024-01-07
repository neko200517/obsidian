## main関数の作成

```bash
touch main.go
```

repsitory, usecase, controller, routerのインスタンスを作成し、各コンストラクタに引数で与え、依存性を注入する。
最後にechoフレームワークのサーバを起動する。

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
	// userRepositoryコンストラクタの起動
	userRepository := repository.NewUserRepository(db)
	// userUsecaseコンストラクタの起動
	userUsecase := usecase.NewUserUsecase(userRepository)
	// userControllerコンストラクタの起動
	userController := controller.NewUserController(userUsecase)
	// routerコンストラクタの起動
	e := router.NewRouter(userController)
	// サーバーの起動（+エラーのロギング、強制終了、ポート8080）
	e.Logger.Fatal(e.Start(":8080"))
}
```

## 動作確認

Postmanなどで動作確認する
### Signup

POST: http://localhost:8080/signup

```json
// Body
{
  "email": "user1@test.com",
  "password": "dummypw"
}
```

レスポンスに201コードが返ってくること

### Login

POST: http://localhost:8080/login 

Cookieにtokenがセットされていること。

### Logout

POST: http://localhost:8080/logout

Cookieに保存されたtokenがクリアされていること。
