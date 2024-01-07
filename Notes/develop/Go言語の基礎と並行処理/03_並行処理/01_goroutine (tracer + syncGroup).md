## goroutine

```go
package main

import (
	"fmt"
	"runtime"
	"sync"
)

// gorutine: trace + syncGroup

func main() {
	// 複数スレッドの管理
	var wg sync.WaitGroup
	wg.Add(1) // スレッドの登録

	// go 関数 = ゴルーチン
	go func() {
		defer wg.Done() // ゴルーチン終了の合図
		fmt.Println("goroutine invoked")
	}()

	// WaitGroupに登録したゴルーチン終了まで待機
	wg.Wait()

	// 起動しているゴルーチンの数量をカウント
	fmt.Printf("num of working gorutines: %d\n", runtime.NumGoroutine()) // 1 = mainゴルーチンのみ
	fmt.Println("main func finish")
}
```

## tracer

処理の順番をトレースするツールによって処理の流れを確認

```go
package main

import (
	"context"
	"fmt"
	"log"
	"os"
	"runtime"
	"runtime/trace"
	"time"
)

// gorutine: trace + syncGroup

// 逐次処理：受け取ったタスクを開始し、1秒おきにタスク名を表示させる
func task(ctx context.Context, name string) {
	// defer チェーンで呼び出した場合、StartRegionは最初に実行、Endは最後に遅延実行される
	defer trace.StartRegion(ctx, name).End()
	time.Sleep(time.Second)
	fmt.Println(name)
}

func main() {
	// トレースの出力結果ファイルを作成
	f, err := os.Create("trace.out")
	if err != nil {
		log.Fatalln("Error:", err)
	}

	// defer: ファイルを閉じる
	defer func() {
		if err := f.Close(); err != nil {
			log.Fatalln("Error:", err)
		}
	}()

	// トレースの開始()
	if err := trace.Start(f); err != nil {
		log.Fatalln("Error:", err)
	}

	// defer: トレース終了
	defer trace.Stop()

	// トレースのタスクを作成
	ctx, t := trace.NewTask(context.Background(), "main") // タスク名mainでバックグラウンドで起動

	// defer: トレースのタスクを終了
	defer t.End()

	// 論理CPUコア数の出力
	fmt.Println("The number of logical CPU Cores:", runtime.NumCPU())

	task(ctx, "Task1")
	task(ctx, "Task2")
	task(ctx, "Task3")
}
```

上記コードを実行するとtrace.outが出力される

### トレースファイルを開く

```bash
go tool trace .\trace.out
```

User-defined tasks → Count → goroutine viewのリンクを開く
逐次処理の場合Task1, Task2, Task3が順番に登録されていることを確認する

### タスク起動の関数をゴルーチンに書き換える

cTaskを実行し、トレーサーを起動してトレース画面を表示すると、Task4, Task5, Task6は並列的に実行されていることが確認できる

```go
package main

import (
	"context"
	"fmt"
	"log"
	"os"
	"runtime"
	"runtime/trace"
	"sync"
	"time"
)

// gorutine: trace + syncGroup

// 逐次処理：受け取ったタスクを開始し、1秒おきにタスク名を表示させる
func task(ctx context.Context, name string) {
	// defer チェーンで呼び出した場合、StartRegionは最初に実行、Endは最後に遅延実行される
	defer trace.StartRegion(ctx, name).End()
	time.Sleep(time.Second)
	fmt.Println(name)
}

// ゴルーチン版：受け取ったタスクを開始し、1秒おきにタスク名を表示させる
// waitGroupは参照として受け取りたいのでポインタとして引数を取得する
func cTask(ctx context.Context, wg *sync.WaitGroup, name string) {
	defer trace.StartRegion(ctx, name).End()
	defer wg.Done() // defer: ゴルーチン完了
	time.Sleep(time.Second)
	fmt.Println(name)
}

func main() {
	// トレースの出力結果ファイルを作成
	f, err := os.Create("trace.out")
	if err != nil {
		log.Fatalln("Error:", err)
	}

	// defer: ファイルを閉じる
	defer func() {
		if err := f.Close(); err != nil {
			log.Fatalln("Error:", err)
		}
	}()

	// トレースの開始()
	if err := trace.Start(f); err != nil {
		log.Fatalln("Error:", err)
	}

	// defer: トレース終了
	defer trace.Stop()

	// トレースのタスクを作成
	ctx, t := trace.NewTask(context.Background(), "main") // タスク名mainでバックグラウンドで起動

	// defer: トレースのタスクを終了
	defer t.End()

	// 論理CPUコア数の出力
	fmt.Println("The number of logical CPU Cores:", runtime.NumCPU())

	// 順次処理
	task(ctx, "Task1")
	task(ctx, "Task2")
	task(ctx, "Task3")

	// マルチスレッド
	var wg sync.WaitGroup
	wg.Add(3)
	go cTask(ctx, &wg, "Task4")
	go cTask(ctx, &wg, "Task5")
	go cTask(ctx, &wg, "Task6")
	wg.Wait()

	fmt.Println("Done")
}
```

### 注意点

```go
    // 中略

	// ゴルーチンの注意点
	// 以下のコードでfor文のマルチスレッド化を行っている
	// ただし表示タイミングですでにforが終わっているため出力は常に3が表示される
	// それを回避するために逐次iの値を引数でとる必要がある
	s := []int{1, 2, 3}
	for _, i := range s {
		wg.Add(1)
		go func(i int) {
			defer wg.Done()
			fmt.Println(i)
		}(i)
	}
	wg.Wait()
```