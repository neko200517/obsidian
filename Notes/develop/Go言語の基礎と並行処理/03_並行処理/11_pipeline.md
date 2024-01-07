## pipeline

チャネルを連携するデザインパターンであるパイプラインを実装する例

```go
package main

import (
	"context"
	"fmt"
)

// int型のスライスをチャネルに変換するジェネレータ関数
func generator(ctx context.Context, nums ...int) <-chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for _, n := range nums {
			select {
			case <-ctx.Done():
				return
			case out <- n:
				//
			}
		}
	}()
	return out
}

// 受け取ったチャネルの値を2倍する関数
func double(ctx context.Context, in <-chan int) <-chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for n := range in {
			select {
			case <-ctx.Done():
				return
			case out <- n * 2:
				//
			}
		}
	}()
	return out
}

// 受け取ったチャネルの値に2を加算する関数
func offset(ctx context.Context, in <-chan int) <-chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for n := range in {
			select {
			case <-ctx.Done():
				return
			case out <- n + 2:
				//
			}
		}
	}()
	return out
}

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()
	nums := []int{1, 2, 3, 4, 5}
	var i int
	flag := true
	out1 := generator(ctx, nums...) // スライスをチャネルに変換
	out2 := double(ctx, out1)       // チャネルの値を2倍
	out3 := offset(ctx, out2)       // チャネルの値を+2
	out4 := double(ctx, out3)       // チャネルの値を2倍

	// 3回目でコンテキストをキャンセルして結果を出力
	// 8 = ((1*2)+2)*2, 12 = ((2*2)+2)*2, 16 = ((3*2)+2)*2, finish
	for v := range out4 {
		if i == 3 {
			cancel()
			flag = false
		}
		if flag {
			fmt.Println(v)
		}
		i++
	}
	fmt.Println("finish")
}
```
