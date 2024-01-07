## errGruop

複数のゴルーチンで発生したエラーを管理する機能。
newを使ってerrorgroup.Groupでエラーグループを作成。
エラーグループはGoの中にゴルーチンの処理を実行する。
Wait()ですべてのゴルーチンの完了を待機してからエラーの集計を行う。

```go
package main

import (
	"fmt"

	"golang.org/x/sync/errgroup"
)

func main() {
	eg := new(errgroup.Group)
	s := []string{"task1", "fake1", "task2", "fake2"}

	// sスライスの全ての要素をゴルーチンで検査する
	for _, v := range s {
		task := v
		eg.Go(func() error {
			return doTask(task)
		})
	}

	// すべてのゴルーチンを完了するまで待機し、errにnil以外のものがあった場合エラー情報を出力
	if err := eg.Wait(); err != nil {
		fmt.Printf("error :%v\n", err)
	}

	fmt.Println("finish")
}

// fake1, fake2の場合エラーを返す
func doTask(task string) error {
	if task == "fake1" || task == "fake2" {
		return fmt.Errorf("%v failed", task)
	}
	fmt.Printf("task %v completed\n", task)
	return nil
}
```

## errGroup + WithContext

errogroup.WithContextでエラーグループからコンテキストを取得
エラーを戻り値にするとコンテキストの一斉キャンセルを行う。

```go
package main

import (
	"context"
	"fmt"
	"time"

	"golang.org/x/sync/errgroup"
)

func main() {
	// errgroup.WithContextでエラーグループとコンテキストを扱える
	eg, ctx := errgroup.WithContext(context.Background())
	s := []string{"task1", "fake1", "task2", "fake2", "task3"}

	// sスライスの全ての要素をゴルーチンで検査する
	for _, v := range s {
		task := v
		eg.Go(func() error {
			return doTask(ctx, task)
		})
	}

	// すべてのゴルーチンを完了するまで待機し、errにnil以外のものがあった場合エラー情報を出力
	if err := eg.Wait(); err != nil {
		fmt.Printf("error :%v\n", err)
	}

	fmt.Println("finish")
}

// fake1, fake2の場合エラーを返す
func doTask(ctx context.Context, task string) error {
	// 受け取った文字列によってtickerの発生速度を変更
	var t *time.Ticker
	switch task {
	case "fake1":
		t = time.NewTicker(500 * time.Millisecond)
	case "fake2":
		t = time.NewTicker(700 * time.Millisecond)
	default:
		t = time.NewTicker(200 * time.Millisecond)
	}

	// コンテキストのキャンセル、受信した処理をそれぞれ実行
	select {
	case <-ctx.Done():
		fmt.Printf("%v cancelled : %v\n", task, ctx.Err())
		return ctx.Err()
	case <-t.C:
		t.Stop()
		if task == "fake1" || task == "take2" {
			return fmt.Errorf("%v process failed", task)
		}
		fmt.Printf("task %v completed\n", task)
	}

	// エラーがなければnilを返す
	return nil
}
```

## コンテキストを親にしたエラーグループ 

context.WithTimeoutでコンテキストを作成し、errgroup.WithContextの引数に与えることでタイムアウトなどの機能を拡張可能

```go
package main

import (
	"context"
	"fmt"
	"time"

	"golang.org/x/sync/errgroup"
)

func main() {
	// 親コンテキストを作成
	ctx, cancel := context.WithTimeout(context.Background(), 800*time.Millisecond)
	defer cancel()

	// errgroup.WithContextに作成したコンテキストを親として作成する
	eg, ctx := errgroup.WithContext(ctx)
	s := []string{"task1", "task2", "task3", "task4"}

	// sスライスの全ての要素をゴルーチンで検査する
	for _, v := range s {
		task := v
		eg.Go(func() error {
			return doTask(ctx, task)
		})
	}

	// すべてのゴルーチンを完了するまで待機し、errにnil以外のものがあった場合エラー情報を出力
	if err := eg.Wait(); err != nil {
		fmt.Printf("error :%v\n", err)
	}

	fmt.Println("finish")
}

// fake1, fake2の場合エラーを返す
func doTask(ctx context.Context, task string) error {
	// 受け取った文字列によってtickerの発生速度を変更
	var t *time.Ticker
	switch task {
	case "task1":
		t = time.NewTicker(500 * time.Millisecond)
	case "task2":
		t = time.NewTicker(700 * time.Millisecond)
	default:
		t = time.NewTicker(1000 * time.Millisecond)
	}

	// コンテキストのキャンセル、受信した処理をそれぞれ実行
	select {
	case <-ctx.Done():
		fmt.Printf("%v cancelled : %v\n", task, ctx.Err())
		return ctx.Err()
	case <-t.C:
		t.Stop()
		fmt.Printf("task %v completed\n", task)
	}

	// エラーがなければnilを返す
	return nil
}
```
