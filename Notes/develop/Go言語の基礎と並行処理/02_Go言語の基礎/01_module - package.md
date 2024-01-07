## module

### 環境変数ファイルを作成

```bash
touch .env
```

```json
// ./env 

GO_ENV=development
```

### 環境変数を読み取って出力する

godotenvという外部パッケージが存在する。
サードパーティー制の外部パッケージはgithub.com/リポジトリ名/パッケージ名という命名規則で管理される。

```go
package main

import (
	"fmt"
	"os"

	"github.com/joho/godotenv"
)

func main() {
	// 環境変数の読み込み
	godotenv.Load() // サードパーティー製のライブラリはgo mod tidyでダウンロードされる
	fmt.Println(os.Getenv("GO_ENV"))
}
```

### 外部パッケージをダウンロードする

外部パッケージ名がソースコードに記述されている場合git mod tidyコマンドで自動でダウンロードされる。

```bash
go mod tidy
```

同じディレクトリにgo.sumというファイルが作成され、go.modファイルには依存パッケージの記述が追加される。

```go
// go.mod

module go-basics

go 1.21.5

require github.com/joho/godotenv v1.5.1
```

## package 

goはパッケージ管理が簡潔に管理可能になっている。
任意のディレクトリを作成し、sum.goとmultiply.goを作成する。

```bash
mkdir calculator
touch calculator/sum.go 
touch calculator/multiply.go
```

外部公開される変数、関数は接頭語が大文字 
パッケージ間の共有変数、関数は接頭語が小文字
という法則をもっている。

### sum.go 

```go
package calculator // 所属させたいパッケージ名

import "fmt"

// グローバル変数：小文字は別パッケージ間で共有される
var offset float64 = 1

// グローバル変数：大文字は外部公開される
var Offset float64 = 2

// 関数：大文字は外部公開される
func Sum(a float64, b float64) float64 {
	fmt.Println("multiply: ", multiply(a, b))
	fmt.Println("Multiply: ", Multiply(a, b))
	return a + b + offset
}
```

### multiply .go 

```go
package calculator // 所属させたいパッケージ名

// 関数：大文字は外部公開される
func Multiply(a float64, b float64) float64 {
	return (a * b) + offset
}

// 関数：小文字は同じパッケージ間で共有される
func multiply(a float64, b float64) float64 {
	return (a * b) + offset
}
```

### main.go 

```go
package main

import (
	"fmt"
	"go-basics/02_go_basics/01_module_package/calculator"　// パッケージの呼び出し
)

func main() {
	// 変数：大文字が外部公開される
	fmt.Println(calculator.Offset)
	
	// ただし外部からは変更不可
	calculator.Offset = 10

	fmt.Println(calculator.Sum(1, 2))
	fmt.Println(calculator.Multiply(1, 2))
}
```

## 外部リンク

https://github.com/joho/godotenv
