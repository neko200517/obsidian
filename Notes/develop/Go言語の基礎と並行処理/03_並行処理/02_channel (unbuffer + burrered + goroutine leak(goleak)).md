## unbeffer channel

チャネル：ゴルーチンをまたがってデータを共有する仕組み。データの受信と送信を行う。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// chanel: ゴルーチン間のデータを送受信するために使用する
// ※バッファ無しチャネルの場合、チャネルの受信側が受信を開始していなければ書き込みのロックは発生しエラーとなる
// チャネルは以下の表記でコードに表現される
// 例）
// チャネルの作成：ch := make(chan int)
// チャネルのデータ受信：println(<-ch)
// チャネルのデータ送信：ch <- 10

func main() {
	ch := make(chan int) // チャネルの作成
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		defer wg.Done()
		ch <- 10 // チャネルの送信
		time.Sleep(500 * time.Microsecond)
	}()
	fmt.Println(<-ch) // チャネルの受信
	wg.Wait()
}
```

## goroutine leak

ゴルーチンが意図しない実行漏れが発生していることをgorutine leak(goleak)と呼ぶ。

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	// ゴルーチンをwaitGroupで適切に処理していないものをgoroutine leakと呼ぶ(goleak)
	// ただしチャネルの特徴として書き込み完了直後、読み込みが即時実行される動作をするのでこの例ではgoleakは発生しない
	ch1 := make(chan int)
	go func() {
		// 1. チャネルのデータを受信開始。データが受信されるまで待機
		// 3. 2が完了したら処理の続行
		fmt.Println(<-ch1)
	}()
	ch1 <- 10 // 2. チャネルにデータ送信
	fmt.Printf("num of working goroutines: %d\n", runtime.NumGoroutine())
}
```

### goleakを検証するためtestを作成する

goleakという外部パッケージがあるので、以下のテストコードを作成して検証する

```bash
touch main_test.go
```

```go
// main_test.go 

package main

import (
	"testing"

	"go.uber.org/goleak"
)

func TestLeak(t *testing.T) {
	defer goleak.VerifyNone(t)
	main()
}
```

### テストの実行

以下のコマンドを実行しテストを開始、goleakが発生しているとエラーになる

```bash
go test .
```

## bufferd channel 

バッファありのチャネルの場合、バッファサイズを超えるまでデータ送信を待機できる

```go
package main

import (
	"fmt"
)

// バッファ付きチャネル：
// チャネルのデータ受信開始前でもデータ送信を貯めることができる。その後データ受信が開始されたらバッファ内のデータを即時取り出し可能

func main() {
	ch := make(chan int, 1) // バッファサイズ1で宣言
	ch <- 2                 // 1. バッファサイズを超えるまでデータ送信を待機
	fmt.Println(<-ch)       // 2. 受信を開始したら1は送信を開始し、データを受信する
}
```
