## generics

```go
package main

import (
	"fmt"

	"golang.org/x/exp/constraints"
)

// 型の制約
// ~をつけることで独自の型にも対応
type customConstraints interface {
	~int | int16 | float32 | float64 | string
}

// 独自のint型
type NewInt int

// ジェネリクス：動的で汎用的な型
// 型に制約を付与
func add[T customConstraints](x, y T) T {
	return x + y
}

// Ordered: Interger | Float | ~string
// Interger: Signed | Usigned
// Signed: ~int | ~int8 | ~int16 | ~int32 | ~int64
// Unsigned: ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 | ~uintptr
// Flaot: ~float32 | ~float64
func min[T constraints.Ordered](x, y T) T {
	if x < y {
		return x
	}
	return y
}

// 合計値の計算
// in:
//
//	map[K] : int | string
//	map[K]V: Float | Integer
//
// out:
//
//	V: Float | Integer
func sumValues[K int | string, V constraints.Float | constraints.Integer](m map[K]V) V {
	var sum V
	for _, v := range m {
		sum += v
	}
	return sum
}

func main() {
	// 様々な型に対応
	fmt.Printf("%v\n", add(1, 2))           // 3
	fmt.Printf("%v\n", add(1.1, 2.1))       // 3.2
	fmt.Printf("%v\n", add("file", ".txt")) // file.txt

	// 独自の型
	var i1, i2 NewInt = 3, 4
	fmt.Printf("%v\n", add(i1, i2)) // 7

	fmt.Printf("%v\n", min(i1, i2)) // 3

	// map
	m1 := map[string]uint{
		"A": 1,
		"B": 2,
		"C": 3,
	}

	m2 := map[string]float32{
		"1": 1.23,
		"2": 4.56,
		"3": 7.89,
	}
	fmt.Println(sumValues(m1)) // 6
	fmt.Println(sumValues(m2)) // 13.68
}
```

## 資料

https://cs.opensource.google/go/x/exp/+/master:constraints/constraints.go