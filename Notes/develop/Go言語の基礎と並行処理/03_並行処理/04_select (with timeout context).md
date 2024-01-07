## select

複数チャネルのそれぞれの処理を分けたい場合はselectを使用する

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	ch1 := make(chan string)
	ch2 := make(chan string)

	var wg sync.WaitGroup
	wg.Add(2)

	go func() {
		defer wg.Done()
		time.Sleep(500 * time.Millisecond)
		ch1 <- "A" // ch1にデータ送信
	}()

	go func() {
		defer wg.Done()
		time.Sleep(800 * time.Millisecond)
		ch2 <- "B" // ch2にデータ送信
	}()

	// ch1とch2両方のデータ受信が完了するまで待機
	for ch1 != nil || ch2 != nil {
		select {
		case v := <-ch1: // ch1にデータが送信されたらこちらが実行される
			fmt.Println(v)
			ch1 = nil
		case v := <-ch2: // ch2にデータが送信されたらこちらが実行される
			fmt.Println(v)
			ch2 = nil
		}
	}

	wg.Wait()
	fmt.Println("finish")
}
```

## select with timeout

context.withTimeoutとselectを使ってタイムアウト処理を実装する

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func main() {
	// タイムアウトでループを終了するときデータ受信を開始していない場合デッドロックが発生するのでバッファ付きで宣言する
	ch1 := make(chan string, 1)
	ch2 := make(chan string, 1)

	var wg sync.WaitGroup

	// 300ms経過後タイムアウト
	ctx, cancel := context.WithTimeout(context.Background(), 600*time.Millisecond)
	defer cancel() // タイムアウトの後処理（メモリの開放など）の遅延処理を実行

	wg.Add(2)
	go func() {
		defer wg.Done()
		time.Sleep(500 * time.Millisecond)
		ch1 <- "A"
	}()

	go func() {
		defer wg.Done()
		time.Sleep(800 * time.Millisecond)
		ch2 <- "B"
	}()

loop:
	for ch1 != nil || ch2 != nil {
		select {
		case <-ctx.Done(): // タイムアウト発生時に実行
			fmt.Println("timeout")
			break loop
		case v := <-ch1:
			fmt.Println(v)
			ch1 = nil
		case v := <-ch2:
			fmt.Println(v)
			ch2 = nil
		}
	}

	wg.Wait()
	fmt.Println("finish")
}
```
