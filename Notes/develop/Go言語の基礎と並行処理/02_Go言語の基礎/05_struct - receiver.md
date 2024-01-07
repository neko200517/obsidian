## struct

いわゆる構造体のこと。実態の作成、値の変更、取得、コピー、ポインタなど

```go
package main

import (
	"fmt"
	"unsafe"
)

// 構造体の宣言
type Task struct {
	Title    string
	Estimate int
}

func main() {
	// 構造体のインスタンスを作成
	task1 := Task{
		Title:    "Learn Golang",
		Estimate: 3,
	}

	// 値の変更
	task1.Title = "Learing Go"

	// 値の取得
	// %+vで構造の名前ごと出力される
	fmt.Printf("%[1]T %+[1]v %v\n", task1, task1.Title) // main.Task {Title:Learning Golang Estimate:3} Learning Golang

	// コピー
	var task2 Task = task1
	task2.Title = "new"
	fmt.Printf("task1: %v task2: %v\n", task1.Title, task2.Title) // task1: Learing Go task2: new

	// ポインタ
	task1p := &Task{
		Title:    "Learn concurrency",
		Estimate: 2,
	}
	fmt.Printf("task1p: %T %+v %v\n", task1p, *task1p, unsafe.Sizeof(task1p)) // task1p: *main.Task {Title:Learn concurrency Estimate:2} 8

	// 値の変更(ポインタ)
	(*task1p).Title = "Changed"
	task1p.Title = "Changed"             // 構造体の場合*省略可能
	fmt.Printf("task1p: %+v\n", *task1p) // task1p: {Title:Changed Estimate:2}

	// task1pのアドレスを共有したtask2pの宣言
	var task2p *Task = task1p
	task2p.Title = "Changed by Task2"
	fmt.Printf("task1p: %+v\n", *task1p) // task1p: {Title:Changed by Task2 Estimate:2}
	fmt.Printf("task2p: %+v\n", *task2p) // task1p: {Title:Changed by Task2 Estimate:2}
}
```

## receiver

構造体の値を変更する場合に利用する関数。非破壊的なレシーバと破壊的なレシーバなど

```go
package main

import "fmt"

type Task struct {
	Title    string
	Estimate int
}

// 非破壊的なレシーバ
func (task Task) extendEstimate() {
	task.Estimate += 10
}

// 破壊的なレシーバ
func (task *Task) extendEstimatePoinetr() {
	task.Estimate += 10
}

func main() {
	task1 := Task{
		Title:    "Learn Golang",
		Estimate: 3,
	}
	// 非破壊的なレシーバの呼び出し
	task1.extendEstimate()
	fmt.Printf("task1 value receiver: %+v\n", task1.Estimate) // 3

	// 破壊的なレシーバの呼び出し
	(&task1).extendEstimatePoinetr()
	task1.extendEstimatePoinetr()                                     // 構造体の場合は&省略可
	fmt.Printf("task1 value receiver pointer: %+v\n", task1.Estimate) // 23
}
```
