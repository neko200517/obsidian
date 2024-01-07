## pointer

```go
package main

import (
	"fmt"
	"unsafe"
)

func main() {
	var ui1 uint16
	fmt.Printf("meory address of ui1: %p\n", &ui1) // %pでポインタ、&でアドレスを参照

	var p1 *uint16                      // *型でポインタ変数の宣言
	fmt.Printf("value of p1: %v\n", p1) // nil

	p1 = &ui1 // p1にui1のアドレスを代入
	fmt.Printf("value of p1: %v\n", p1)

	// ポインタ
	fmt.Printf("size of p1: %d[byte]\n", unsafe.Sizeof(p1)) // 8byte
	fmt.Printf("memory address of p1: %p\n", &p1)           // p1のアドレス
	fmt.Printf("value of ui1(dereference): %v\n", *p1)      // p1に格納されているui1の値の参照（dereference）
	*p1 = 1
	fmt.Printf("value of ui1: %v\n", ui1) // ui1: 1

	// ダブルポインタ
	var pp1 **uint16 = &p1
	fmt.Printf("value of pp1: %v\n", pp1)                     // pp1: p1のアドレス
	fmt.Printf("size of pp1: %d[byte]\n", unsafe.Sizeof(pp1)) // 8byte
	fmt.Printf("memory address of pp1: %p\n", &pp1)           // pp1のアドレス
	fmt.Printf("value of p1(dereference): %v\n", *pp1)        // pp1を1回dereferece: p1のアドレス
	fmt.Printf("value of ui1(dereference): %v\n", **pp1)      // pp1を2回dereferece: p1をdereferece → ui1の値: 1
	**pp1 = 10
	fmt.Printf("value of ui1: %v\n", ui1) // ui1: 10
}
```

## shadowing

スコープの外側の変数を隠したいときに利用する

```go
package main

import "fmt"

func main() {
	// if文resultと外のresultではアドレスが異なるので別物として独立している
	ok, result := true, "A"
	fmt.Printf("memory address of result(scope 1): %p\n", &result) // アドレスA
	if ok {
		result := "B"
		fmt.Printf("memory address of result(scope 2): %p\n", &result) // アドレスB
		println(result)                                                // B
	} else {
		result := "C"
		println(result)
	}
	println(result) // A

	// 同じアドレスを共有したい場合は、if文の中の:を省くことで実現する
	fmt.Printf("memory address of result(scope 1): %p\n", &result) // アドレスA
	if ok {
		result = "B"
		fmt.Printf("memory address of result(scope 2): %p\n", &result) // アドレスA
		println(result)                                                // B
	} else {
		result = "C"
		println(result)
	}
	println(result) // B
}
```
