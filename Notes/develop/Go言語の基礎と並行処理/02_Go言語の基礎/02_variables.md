## variables

var、型推論による変数の宣言、
整数型、浮動小数点型、文字列型、bool型、型の変換、定数など

```go
package main

import "fmt"

// 定数
const secret = "abc"

// 型の定義
type Os int

// constの複数行定義によって1行目と同じ型とiotaにより連番が生成される
const (
	Mac Os = iota + 1
	Windows
	Linux
)

// varの複数行宣言
var (
	i int    // 初期値：0
	s string // 初期値：""
	b bool   // 初期値：false
)

func main() {
	var i int // 0で初期化
	fmt.Println(i)

	var j = 2 // var付きでも型を省略すると推定される
	fmt.Println(j)

	// 型推論
	k := 1
	ui := uint(16)
	fmt.Println(k)

	// 書式付き出力
	fmt.Printf("i: %v %T\n", i, i)                        // i: 0 int
	fmt.Printf("i: %[1]v %[1]T ui: %[2]v %[2]T\n", i, ui) // i: 0 int ui: 16 unit

	f := 1.23456
	s := "hello"
	b := true
	fmt.Printf("f: %[1]v %[1]T\n", f) // 1.23456 floag64
	fmt.Printf("s: %[1]v %[1]T\n", s) // hello string
	fmt.Printf("b: %[1]v %[1]T\n", b) // true bool

	// 複数の宣言
	pi, title := 3.14, "Go"
	fmt.Printf("pi: %v title: %v\n", pi, title) // pi: 3.14 title: Go

	// 型の変換
	x := 10
	y := 1.23
	z := float64(x) + y
	fmt.Printf("z: %v\n", z) // z: 11.23

	// 定数の出力
	fmt.Printf("Mac:%v Widnows:%v Linux:%v\n", Mac, Windows, Linux) // Mac:1 Windows:2 Linux:3

	// 変数の変更
	i = 2
	fmt.Printf("i: %v\n", i) // i: 2
	i += 1
	fmt.Printf("i: %v\n", i) // i: 3
}
```