## repositoryの作成

```bash
mkdir repository 
touch repository/user_repository.go
```

## interfaceを作成

```go
// repository/user_repository.go

package repository

import "go-echo/model"

type IUserRepository interface {
	GetUserByEmail(user *model.User, email string) error
	CreateUser(user *model.User) error
}
```

## interfaceの実装

DBからユーザーを取得、DBにユーザーを作成

```go
package repository

import (
	"go-echo/model"

	"gorm.io/gorm"
)

// user_repositoryのインタフェース
type IUserRepository interface {
	GetUserByEmail(user *model.User, email string) error
	CreateUser(user *model.User) error
}

// user_repositoryの構造体
type userRepository struct {
	db *gorm.DB
}

// user_repositoryの依存性の注入
func NewUserRepository(db *gorm.DB) IUserRepository {
	return &userRepository{db}
}

// user_repositoryのGetUserByEmailの実装
func (ur *userRepository) GetUserByEmail(user *model.User, email string) error {
	// DBにアクセスしemailに一致するユーザーを取得
	// Firstでuserのポインタに参照を渡す
	if err := ur.db.Where("email=?", email).First(user).Error; err != nil {
		return err
	}
	return nil
}

// user_repositoryのGetUserByEmailの実装
func (ur *userRepository) CreateUser(user *model.User) error {
	if err := ur.db.Create(user).Error; err != nil {
		return err
	}
	return nil
}
```
