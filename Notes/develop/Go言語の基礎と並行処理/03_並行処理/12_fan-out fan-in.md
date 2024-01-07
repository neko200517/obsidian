## fan-out fan-in

fan-out: ひとつの処理を各ゴルーチンに分割しチャネルに出力する
fan-in: 分割したチャネルをひとつにまとめる

```go
package main

import (
	"context"
	"fmt"
	"runtime"
	"sync"
	"time"
)

func generator(ctx context.Context, nums ...int) <-chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for _, n := range nums {
			select {
			case <-ctx.Done():
				return
			case out <- n:
			}
		}
	}()
	return out
}

// 複数のゴルーチンからチャネルに送信
func fanOut(ctx context.Context, in <-chan int, id int) <-chan string {
	out := make(chan string)
	go func() {
		defer close(out)

		// 重い処理をシミュレートした関数（200ms）
		heaveyWork := func(i int, id int) string {
			time.Sleep(200 * time.Millisecond)
			return fmt.Sprintf("result: %v (id: %v)", i*i, id) // フォーマットした文字列を返す
		}

		for v := range in {
			select {
			case <-ctx.Done():
				return
			case out <- heaveyWork(v, id): // 重い処理の結果を出力チャネルに送信
			}
		}
	}()
	return out
}

// 並行処理でチャネルに送信し、すべての処理が完了したらまとめて結果を出力
func fanIn(ctx context.Context, chs ...<-chan string) <-chan string {
	var wg sync.WaitGroup
	out := make(chan string)

	// 結果をチャネルに送信
	multiplex := func(ch <-chan string) {
		defer wg.Done()
		for text := range ch {
			select {
			case <-ctx.Done():
				return
			case out <- text:
			}
		}
	}

	// 結果の数だけゴルーチンの起動
	wg.Add(len(chs))
	for _, ch := range chs {
		go multiplex(ch)
	}

	// すべて完了するまで待機
	go func() {
		wg.Wait()
		close(out)
	}()

	return out
}

func main() {
	cores := runtime.NumCPU() // CPUのロジカルコア数を取得
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()
	nums := []int{1, 2, 3, 4, 5, 6, 7, 8}

	outChs := make([]<-chan string, cores) // バッファの数はCPUのロジカルコア数分用意する
	inData := generator(ctx, nums...)

	// ロジカルコアの分だけゴルーチンを起動し、文字列の結果をチャネル型の配列に格納
	for i := 0; i < cores; i++ {
		outChs[i] = fanOut(ctx, inData, i+1)
	}

	// チャネル型の配列の分だけゴルーチンを起動し、結果を出力
	var i int
	flag := true
	for v := range fanIn(ctx, outChs...) {
		// n回目でコンテキストをキャンセル
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

## 結果 

結果は以下のようになり、fan-outで登録されたidとresultが一致していないことからfan-out毎にゴルーチンが起動し、各タスクごとに並行処理が行われていることを意味している。

```bash
result: 4 (id: 2)
result: 36 (id: 8)
result: 16 (id: 6)
finish
```
