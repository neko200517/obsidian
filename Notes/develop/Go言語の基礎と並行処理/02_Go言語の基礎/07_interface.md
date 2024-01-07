## interface

インタフェース型、インタフェースの実装、any型など

```go
package main

import (
	"fmt"
	"unsafe"
)

// インタフェースで関数の宣言
type controller interface {
	speedUp() int
	speedDown() int
}

type vehicle struct {
	speed       int
	enginePower int
}

type bycycle struct {
	speed      int
	humanPower int
}

// レシーバでインタフェースの実装
func (v *vehicle) speedUp() int {
	v.speed += 10 * v.enginePower
	return v.speed
}

func (v *vehicle) speedDown() int {
	v.speed -= 5 * v.enginePower
	return v.speed
}

func (v *bycycle) speedUp() int {
	v.speed += 3 * v.humanPower
	return v.speed
}

func (v *bycycle) speedDown() int {
	v.speed -= 1 * v.humanPower
	return v.speed
}

// インタフェースを介して同じ関数を持つ構造体にアクセス
func speedUpAndDown(c controller) {
	fmt.Printf("%T is current speed: %v\n", c, c.speedUp())
	fmt.Printf("%T is current speed: %v\n", c, c.speedDown())
}

// StringerインタフェースのString()メソッドを受け継いでいるため、Stringerインタフェースの実態とみなされる
func (v vehicle) String() string {
	return fmt.Sprintf("Vehicle current speed is %v (enginPower: %v)", v.speed, v.enginePower)
}

// anyを使った型チェック関数
func checkType(i any) {
	switch i.(type) { //　変数.(type): 型の取得
	case nil:
		fmt.Println("nil")
	case int:
		fmt.Println("int")
	case string:
		fmt.Println("string")
	default:
		fmt.Println("unknown")
	}
}

func main() {
	v := &vehicle{0, 5}
	speedUpAndDown(v)

	b := &bycycle{0, 5}
	speedUpAndDown(b)

	// Stringerインタフェースを継承しているためPrintlnの引数に指定した時上記関数のオーバーライドが可能
	fmt.Println(v)

	// any型：型が特定できない場合に使用する
	// anyと空のinerfaceどちらも同じ意味を持つ
	var i1 interface{}
	var i2 any
	fmt.Printf("%[1]v %[1]T %v\n", i1, unsafe.Sizeof(i1)) // <nil> <nil> 16
	fmt.Printf("%[1]v %[1]T %v\n", i2, unsafe.Sizeof(i2)) // <nil> <nil> 16
	checkType(i2)                                         // nil
	i2 = 1
	checkType(i2) // int
	i2 = "Hello"
	checkType(i2) // string
}
```
