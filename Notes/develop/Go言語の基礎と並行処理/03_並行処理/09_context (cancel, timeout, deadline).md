## WithTimeout

context.WithTimeout：タイムアウト時間の設定
引数　：親プロセスの指定、タイムアウト時間
戻り値：out: ctx, cancel

ctx.Done()をselectでキャッチするとタイムアウトを検知。
defer cancel()で後処理を実行。
タイムアウトされらたらctx.Error()でエラーを取得（context deadline exceeded）。

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func subTask(ctx context.Context, wg *sync.WaitGroup, id string) {
	defer wg.Done()
	t := time.NewTicker(500 * time.Millisecond) // 500ms感覚でチャネルを送信（Cで受信）

	select {
	case <-ctx.Done():
		fmt.Println(ctx.Err())
		return
	case <-t.C: // tickerから受信するチャネル
		t.Stop() // tickerの停止
		fmt.Println(id)
	}
}

func main() {
	var wg sync.WaitGroup
	ctx, cancel := context.WithTimeout(context.Background(), 600*time.Millisecond)
	defer cancel()

	wg.Add(3)
	go subTask(ctx, &wg, "a")
	go subTask(ctx, &wg, "b")
	go subTask(ctx, &wg, "c")
	wg.Wait()
}
```

## WithCancel

main側でコンテキストの一斉キャンセルを行う。
context.WithCancelの戻り値ctxをサブルーチンに渡して、context.WithTimeoutの引数に渡すことで呼び出し元で派生するcontextを一斉キャンセルすることが可能。

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

// タスク：タイムアウト発生
func criticalTask(ctx context.Context) (string, error) {
	ctx, cancel := context.WithTimeout(ctx, 1200*time.Millisecond)
	defer cancel()
	t := time.NewTicker(1000 * time.Millisecond)

	select {
	case <-ctx.Done():
		return "", ctx.Err() // タイムアウトが起こったときの戻り値
	case <-t.C:
		t.Stop() // tickerから受信したチャネルを受け取ったときtickerを停止
	}

	return "A", nil // 正常な戻り値
}

// タスク：正常なタスク
func normalTask(ctx context.Context) (string, error) {
	t := time.NewTicker(3000 * time.Millisecond)
	select {
	case <-ctx.Done():
		return "", ctx.Err()
	case <-t.C:
		t.Stop()
	}
	return "B", nil
}

func main() {
	var wg sync.WaitGroup
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	wg.Add(1)
	go func() {
		defer wg.Done()
		v, err := criticalTask(ctx)
		if err != nil {
			fmt.Printf("critical task cancelled due to: %v\n", err)
			cancel() // コンテキストの一斉キャンセル
			return
		}
		fmt.Println("success", v)
	}()

	wg.Add(1)
	go func() {
		defer wg.Done()
		v, err := normalTask(ctx)
		if err != nil {
			fmt.Printf("normal task cancelled due to: %v\n", err)
			return
		}
		fmt.Println("success", v)
	}()

	wg.Wait()
}
```

## WithDeadline

時刻を指定してコンテキストをキャンセルする場合に利用する

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func subTask(ctx context.Context) <-chan string {
	ch := make(chan string)

	go func() {
		defer close(ch)
		deadline, ok := ctx.Deadline()
		if ok {
			// デッドラインを超えた場合
			if deadline.Sub(time.Now().Add(30*time.Millisecond)) < 0 {
				fmt.Println("impossible to meet deadline")
				return
			}
		}
		// デッドラインを超えていない場合
		time.Sleep(30 * time.Millisecond)
		ch <- "hello"
	}()
	return ch
}

func main() {
	// 現在時刻の40ms後がデッドラインとなる
	ctx, cancel := context.WithDeadline(context.Background(), time.Now().Add(40*time.Millisecond))
	defer cancel()
	ch := subTask(ctx)
	v, ok := <-ch
	if ok {
		fmt.Println(v)
	}
	fmt.Println("finish")
}
```