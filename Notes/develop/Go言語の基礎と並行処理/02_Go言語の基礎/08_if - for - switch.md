## if - for - switch

```go
package main

import (
	"fmt"
	"time"
)

type item struct {
	price float32
}

func main() {
	// if
	a := -1
	if a == 0 {
		println("zero")
	} else if a > 0 {
		println("positive")
	} else {
		println("negative")
	}

	// for
	for i := 0; i < 5; i++ {
		println(i)
	}

	// 無限ループ
	// for {
	// 	println("working")
	// 	time.Sleep(2 * time.Second) // 2秒おきにスリープ
	// }

	// break
	var i int
	for {
		if i > 3 {
			break
		}
		println(i)
		i += 1
		time.Sleep(300 * time.Millisecond) // 2ミリ秒おきにスリープ
	}

	// label, switch, continue
loop:
	for i := 0; i < 10; i++ {
		switch i {
		case 2:
			continue
		case 3:
			continue
		case 8:
			break loop // ただのbreakだとfor文を抜けないためラベルをつけた全体をbreakする
		default:
			fmt.Printf("%v ", i)
		}
	}
	println()

	// for range
	items := []item{
		{price: 10.},
		{price: 20.},
		{price: 30.},
	}
	for _, i := range items {
		i.price *= 1.1
	}
	fmt.Printf("%+v\n", items) // [{price:10} {price:20} {price:30}]

	for i := range items {
		items[i].price *= 1.1
	}
	fmt.Printf("%+v\n", items) // [{price:11} {price:22} {price:33}]
}
```
