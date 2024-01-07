## プロジェクトの作成 

```bash
go mod init
```

## 環境変数ファイルの作成

```bash
touch .env
```

### .env

```env
PORT=8080
POSTGRES_USER=root
POSTGRES_PW=root
POSTGRES_DB=golang
POSTGRES_PORT=5432
POSTGRES_HOST=localhost
SECRET=uu5pveql
GO_ENV=dev
API_DOMAIN=localhost
FE_URL=http://localhost:3000
```

あらかじめrootというユーザーを作成し、テーブル作成の権限を与えておく

## model

テーブルの構造を作成する

```bash
mkdir model 
touch model/user.go 
touch model/task.go
```

以下のコードを参考に構造体を定義する。
json：小文字のフィールド名,
gorm：制約
### user.go

```go
// user.go
package model

import "time"

// テーブルの構造
type User struct {
	ID        uint      `json:"id" gorm:"primaryKey"`
	Email     string    `json:"email" gorm:"unique"`
	Password  string    `json:"password"`
	CreatedAt time.Time `json:"created_at"`
	UpdatedAt time.Time `json:"updated_at"`
}

// レスポンスの構造
type UserRespose struct {
	ID    uint   `json:"id" gorm:"primaryKey"`
	Email string `json:"email" gorm:"unique"`
}
```

### task.go

外部キー：
　json：紐づけるテーブル 
　foreignKey：Userテーブルの主キーと紐づけるフィールド名 + 制約

```go
// task.go 

package model

import "time"

type Task struct {
	ID        uint      `json:"id" gorm:"primaryKey"`
	Title     string    `json:"title" gorm:"not null"`
	CreatedAt time.Time `json:"created_at"`
	UpdatedAt time.Time `json:"updated_at"`
	User      User      `json:"user" gorm:"foreignKey:UserId; constraint:OnDelete:CASCADE"`
	UserId    uint      `json:"user_id" gorm:"not null"`
}

type TaskResponse struct {
	ID        uint      `json:"id" gorm:"primaryKey"`
	Title     string    `json:"title" gorm:"not null"`
	CreatedAt time.Time `json:"created_at"`
	UpdatedAt time.Time `json:"updated_at"`
}
```

## db

```bash
mkdir db
touch db/db.go
```

### db.go 

```go
// db.go 

package db

import (
	"fmt"
	"log"
	"os"

	"github.com/joho/godotenv"
	"gorm.io/driver/postgres"
	"gorm.io/gorm"
)

// DBを開く
func NewDB() *gorm.DB {
	// 環境変数（.env）の読込
	if os.Getenv("GO_ENV") == "dev" {
		err := godotenv.Load()
		if err != nil {
			log.Fatalln(err)
		}
	}

	// 接続文字列
	url := fmt.Sprintf("postgres://%s:%s@%s:%s/%s", os.Getenv("POSTGRES_USER"),
		os.Getenv("POSTGRES_PW"), os.Getenv("POSTGRES_HOST"),
		os.Getenv("POSTGRES_PORT"), os.Getenv("POSTGRES_DB"))

	// 接続
	db, err := gorm.Open(postgres.Open(url), &gorm.Config{})

	if err != nil {
		log.Fatalln(err)
	}

	fmt.Println("Connected")
	return db
}

// DBを閉じる
func CloseDB(db *gorm.DB) {
	sqlDB, _ := db.DB()
	if err := sqlDB.Close(); err != nil {
		log.Fatalln(err)
	}
}
```

外部パッケージのインストールは以下のコマンドを実行する。

```bash
go mod tidy
```

## migrage

```bash
mkdir migrate
touch migrate/migrate.go
```

### migrate.go 

```go
// migrate.go 

package main

import (
	"fmt"
	"go-echo/db"
	"go-echo/model"
)

func main() {
	dbConn := db.NewDB()
	defer fmt.Println("Successfully Migrated")
	defer db.CloseDB(dbConn)
	dbConn.AutoMigrate(&model.User{}, &model.Task{})
}
```

以下のコマンドでマイグレーションを実行する。

```bash
set GO_ENV=dev 
go run migrate/migrate.go
```
