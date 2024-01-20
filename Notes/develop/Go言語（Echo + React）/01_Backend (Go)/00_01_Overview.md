## リポジトリ

```bash
git clone https://github.com/neko200517/go-rest-api
```

## ファイル構成

```bash
.
├── app
│   ├── README.md
│   ├── controller
│   │   ├── task_controller.go
│   │   └── user_controller.go
│   ├── db
│   │   └── db.go
│   ├── go.mod
│   ├── go.sum
│   ├── main.go
│   ├── migrate
│   │   └── migrate.go
│   ├── model
│   │   ├── task.go
│   │   └── user.go
│   ├── repository
│   │   ├── task_repository.go
│   │   └── user_repository.go
│   ├── router
│   │   └── router.go
│   ├── usecase
│   │   ├── task_usecase.go
│   │   └── user_usecase.go
│   └── validator
│       ├── task_validator.go
│       └── user_validator.go
├── build
│   ├── app
│   │   └── Dockerfile
│   └── db
│       ├── Dockerfile
│       └── init
│           └── init.sql
└── docker-compose.yml
```
