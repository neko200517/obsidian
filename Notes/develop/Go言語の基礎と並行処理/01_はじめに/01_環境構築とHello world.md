## VSCodeの拡張

- Prettier Code formatter
- Go

## VSCodeの設定

1. Ctrl + P → Go: Install/Update ToosでToolsにすべてチェックをいれてインストール
2. Ctrl + P → Open User Settings でユーザー設定ファイルを開いて以下の設定を追加する

```json
{
 // 中略
  "[go]": {
    "editor.defaultFormatter": "golang.go",
    "editor.formatOnSave": true
  }
}
```

##  プロジェクトの作成

go mod init コマンドでプロジェクトファイルの作成

```
mkdir <GOLANG_PATH>/src/go-lang
cd <GOLANG_PATH>/src/go-lang 
go mod init go-basics
touch main.go
```

## Hello world

```go
package main

import "fmt"

func main() {
	fmt.Println("hello world")
	sl := []int{1, 2, 3}
	if len(sl) > 2 {
		fmt.Println("unreachable code")
	}
}
```

## Run

```bash
go run ./main.go
```

## Build

```bash
go build -o app.exe ./main.go
```
