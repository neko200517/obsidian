## function

関数の引数、戻り値、return、deferの遅延実行、無名関数、関数型の引数、関数型の戻り値など

```bash
touch file.txt
```

```go
package main

import (
	"errors"
	"os"
	"strings"
)

func funcDefer() {
	// deferをつけた処理はreturnを返した後に遅れて実行される
	// 主な用途してはファイル開放などの必ず実行するべき後処理に用いる
	// 複数deferがある場合、下から順に実行される
	defer println("3. main func final-finish")
	defer println("2. main func semi-finish")
	println("1. hello world")
}

// 渡された配列から末尾の.csvを削除して返却する関数
// in: []string
// out: []string
func trimExtension(files ...string) []string {
	out := make([]string, 0, len(files))
	for _, f := range files {
		out = append(out, strings.TrimSuffix(f, ".csv"))
	}
	return out
}

// ファイルの存在確認
// in: string
// out: string, error
func fileChecker(name string) (string, error) {
	f, err := os.Open(name)
	if err != nil {
		return "", errors.New("file not found") // 文字列を設定してerror型を返却
	}
	defer f.Close() // return後に実行
	return name, nil
}

// 受け取った関数に引数nameを引数にして実行
// in: f: func(file string), name: string
func addExt(f func(file string) string, name string) {
	println(f(name))
}

// 無名関数を返却する関数
func multiply() func(int) int {
	return func(n int) int {
		return n * 1000
	}
}

func main() {
	// 出力結果
	// 1. hello world
	// 2. main func semi-finish
	// 3. main func final-finish
	funcDefer()

	// 末尾の.csvを削除
	files := []string{"file1.csv", "file2.csv", "file3.csv"}
	println(trimExtension(files...))

	// ファイルの存在確認
	name, err := fileChecker("file.txt")
	if err != nil {
		println(err)
		return
	}
	println(name) // file.txt

	// 無名関数
	// 関数名は不要、末尾に(変数)を付与して実行
	i := 1
	func(i int) {
		println(i) // 1
	}(i)

	// 即時実行しない無名関数
	// 変数に関数を格納し、後で変数()で実行
	f1 := func(i int) int {
		return i + 1
	}
	println(f1(i)) // 2

	// 無名関数を渡して呼び出し先で無名関数を実行するパターン
	// 無名関数をaddExtに渡して呼び出し先で無名関数の実行
	f2 := func(file string) string {
		return file + ".csv"
	}
	addExt(f2, "file1") // file1.csv

	// 無名関数を受け取って定義元の無名関数を実行するパターン
	f3 := multiply()
	println(f3(2)) // 2000
}
```

## closure

無名関数を呼び出し元で実行することで、関数内の変数初期化を毎回行わずに変数の値を保持し続ける手法

```go
package main

// クロージャ：変数の値を保持しつつ関数内のスコープに閉じることができる
func countUp() func(int) int {
	count := 0               // 初期化されるのは初回の1回のみ
	return func(n int) int { // 移行はこちらが呼ばれるのでcountの値は保持される
		count += n
		return count
	}
}

func countUp_noCloser(n int) int {
	count := 0 // 毎回countが初期化される
	count += n
	return count
}

func main() {
	// countUpを5回実行
	for i := 1; i <= 5; i++ {
		v2 := countUp_noCloser(2)
		println(v2) // 2 2 2 2
	}

	// countUpを5回実行（値の保持）
	f := countUp()
	for i := 1; i <= 5; i++ {
		v := f(2)
		println(v) // 2 4 6 8
	}
}
```