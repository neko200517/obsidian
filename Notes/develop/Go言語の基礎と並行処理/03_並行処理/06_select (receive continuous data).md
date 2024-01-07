## マルチタスクのタイムアウト処理とデータ受信の続行

ゴルーチンを3つ実行し、それぞれ値をカウントしていくプログラム。
ひとつはチャネルの監視を行い、各チャネルごとにチャネルが閉じられるまで現在の値の出力を繰り返す。
タイムアウトが発生しても処理を続行するため、タイムアウト後のselect caseにさらにチャネルの監視ループをネストして実行する。

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

const bufSize = 5

// 指定の時間待機して値をカウントしてチャネルにデータ送信される
// chan<-: 書き込み専用チャネル
// size: バッファサイズ
func countProducer(wg *sync.WaitGroup, ch chan<- int, size int, sleep int) {
	defer wg.Done()
	defer close(ch)
	for i := 0; i < size; i++ {
		time.Sleep(time.Duration(sleep) * time.Millisecond)
		ch <- i
	}
}

// 値を受信する
// チャネルからデータを受信したら順次表示を行い、タイムアウトしたらエラー表示して処理を中断する
func countConsumer(ctx context.Context, wg *sync.WaitGroup, ch1 <-chan int, ch2 <-chan int) {
	defer wg.Done()
	for ch1 != nil || ch2 != nil {
		select {
		case <-ctx.Done():
			// contextがタイムアウトした場合
			fmt.Println(ctx.Err())

			// タイムアウト後もデータの受信処理を続行する
			for ch1 != nil || ch2 != nil {
				select {
				case v, ok := <-ch1:
					// ch1を受信、closeした場合
					if !ok {
						// closeした場合
						ch1 = nil
						break
					}
					fmt.Printf("ch1 %v\n", v)
				case v, ok := <-ch2:
					// ch2を受信、closeした場合
					if !ok {
						// closeした場合
						ch2 = nil
						break
					}
					fmt.Printf("ch2 %v\n", v)
				}
			}
		case v, ok := <-ch1:
			// ch1を受信、closeした場合
			if !ok {
				// closeした場合
				ch1 = nil
				break
			}
			fmt.Printf("ch1 %v\n", v)
		case v, ok := <-ch2:
			// ch2を受信、closeした場合
			if !ok {
				// closeした場合
				ch2 = nil
				break
			}
			fmt.Printf("ch2 %v\n", v)
		}
	}
}

func main() {
	ch1 := make(chan int, bufSize)
	ch2 := make(chan int, bufSize)
	var wg sync.WaitGroup

	// タイムアウト（3000ms）
	ctx, cancel := context.WithTimeout(context.Background(), 180*time.Millisecond)
	defer cancel()

	wg.Add(3)
	go countProducer(&wg, ch1, bufSize, 50)
	go countProducer(&wg, ch2, bufSize, 500)
	go countConsumer(ctx, &wg, ch1, ch2)

	wg.Wait()
	fmt.Println("finish")
}
```