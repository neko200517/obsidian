## usecaseを作成

```bash
mkdir usecase
touch usecase/user_usecase.go
```

## interfaceを作成

```go
// repository/user_repository.go

package usecase

import (
	"go-echo/model"
)

// user_usercaseのインタフェース
type IUserUsecase interface {
	SignUp(user model.User) (model.UserRespose, error)
	Login(user model.User) (string, error)
}
```

## interfaceの実装

サインアップとログイン機能の実装

```go
package usecase

import (
	"go-echo/model"
	"go-echo/repository"
	"os"
	"time"

	"github.com/golang-jwt/jwt/v4"
	"golang.org/x/crypto/bcrypt"
)

// user_usercaseのインタフェース
type IUserUsecase interface {
	SignUp(user model.User) (model.UserRespose, error)
	Login(user model.User) (string, error)
}

// user_usercaseの構造体
type userUsecase struct {
	ur repository.IUserRepository
}

// user_usercaseの依存性の注入
func NewUserUsecase(ur repository.IUserRepository) IUserUsecase {
	return &userUsecase{ur}
}

// user_usercaseのSingnUpの実装
func (uu *userUsecase) SignUp(user model.User) (model.UserRespose, error) {
	// 平文のパスワードをハッシュ化
	hash, err := bcrypt.GenerateFromPassword([]byte(user.Password), 10)
	if err != nil {
		return model.UserRespose{}, err
	}

	// メールとハッシュ化したパスワードを登録した新しいユーザの作成
	newUser := model.User{Email: user.Email, Password: string(hash)}

	// DBに新しいユーザーを登録
	if err := uu.ur.CreateUser(&newUser); err != nil {
		return model.UserRespose{}, err
	}

	// 新しいユーザーを元にユーザーレスポンスを作成
	resUser := model.UserRespose{
		ID:    newUser.ID,
		Email: newUser.Email,
	}

	return resUser, nil
}

// user_usecaseのLoginの実装
// 戻り値：jwtトークン
func (uu *userUsecase) Login(user model.User) (string, error) {
	// DBからユーザーを取得・存在確認
	storedUser := model.User{}
	if err := uu.ur.GetUserByEmail(&storedUser, user.Email); err != nil {
		return "", err
	}

	// パスワードの検証
	err := bcrypt.CompareHashAndPassword([]byte(storedUser.Password), []byte(user.Password))
	if err != nil {
		return "", err
	}

	// jwtトークンの生成
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, jwt.MapClaims{
		"user_id": storedUser.ID,
		"exp":     time.Now().Add(time.Hour * 12).Unix(), // 有効期限（12h）
	})
	tokenString, err := token.SignedString([]byte(os.Getenv("SECRET"))) // SECRETに基づいてtokenの作成
	if err != nil {
		return "", err
	}

	return tokenString, nil
}
```
