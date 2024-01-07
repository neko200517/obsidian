## インストール

```bash
scoop install go
```

## VSCode

・VS Codeの拡張機能「Go」をインストールする
・Ctrl + P → Go: Install/Update Tools ですべてのツールをインストールする

## ソースコードの作成

```bash
mkdir <GOPATH>/src/golang/
touch <GOPATH>/src/golang/main.go
```
## 最初のコード

```go
// src/golang/lesson01/main.go

package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("Hello World.")
	fmt.Println(time.Now())
}
```

## 起動

```bash
go run ./main.go
```

## ビルド

```bash
go build -o main.exe main.go
```
