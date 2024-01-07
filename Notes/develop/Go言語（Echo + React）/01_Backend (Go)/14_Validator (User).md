## validator

### ファイルを作成

```bash
touch validator/user_validator.go
```

### 外部パッケージ

go-ozzo-validationを使用する

### user_validator.go

```go
// validator/user_validator.go 

package validator

import (
	"go-echo/model"

	validation "github.com/go-ozzo/ozzo-validation/v4"
	"github.com/go-ozzo/ozzo-validation/v4/is"
)

type IUserValidator interface {
	UserValidate(user model.User) error
}

type userValidator struct{}

func NewUserValidator() IUserValidator {
	return &userValidator{}
}

// バリデーションの実装
func (uv *userValidator) UserValidate(user model.User) error {
	return validation.ValidateStruct(&user,
		validation.Field(
			&user.Email, // Email
			validation.Required.Error("email is required"),            // 必須
			validation.RuneLength(1, 30).Error("limited max 30 char"), // 1～30文字
			is.Email.Error("is not valid email format"),               // Emailフォーマットの検証
		),
		validation.Field(
			&user.Password, // Password
			validation.Required.Error("password is required"),               // 必須
			validation.RuneLength(6, 30).Error("limited min 6 max 30 char"), // 6～30文字
		),
	)
}
```

## usecaseに組み込み

### user_usecase.go 

#### userStructに追加

```go
// usecase/user_usecase.go 

// 中略
type userUsecase struct {
	ur repository.IUserRepository
	uv validator.IUserValidator // バリデーション
}
// 中略
```

#### NewUserUsecaseに追加

```go
// usecase/user_usecase.go 

// 中略
func NewUserUsecase(ur repository.IUserRepository, uv validator.IUserValidator) IUserUsecase {
	return &userUsecase{ur, uv}
}
// 中略
```

#### Signupに追加

```go
// usecase/user_usecase.go 

// 中略
func (uu *userUsecase) SignUp(user model.User) (model.UserRespose, error) {
	// バリデーションチェック
	if err := uu.uv.UserValidate(user); err != nil {
		return model.UserRespose{}, err
	}
	// 中略
}
```

#### Loginに追加 

```go
// usecase/user_usecase.go 

// 中略
func (uu *userUsecase) Login(user model.User) (string, error) {
	// バリデーションチェック
	if err := uu.uv.UserValidate(user); err != nil {
		return "", err
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
	userValidator := validator.NewUserValidator()

	// task関係のインスタンスを生成
	userRepository := repository.NewUserRepository(db)
	userUsecase := usecase.NewUserUsecase(userRepository, userValidator) // 追加
	userController := controller.NewUserController(userUsecase)

    // 中略
}
```
