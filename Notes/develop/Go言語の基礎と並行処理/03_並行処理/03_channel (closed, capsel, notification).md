## closed

チャネルを閉じる。閉じたチャネルはデータの送受信不可能だが、バッファ無しとバッファありのチャネルで動作が異なる。

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	// バッファ無しチャネルの場合
	// closeされたら即座にチャネルを閉じてデータの受信は不可
	ch1 := make(chan int)
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		defer wg.Done()
		fmt.Println(<-ch1)
	}()
	ch1 <- 10
	close(ch1) // チャネルを閉じる

	v, ok := <-ch1               // 値、状態
	fmt.Printf("%v %v\n", v, ok) // 0 false
	wg.Wait()

	// バッファありチャネルの場合
	// バッファの残りがある場合はcloseされてもデータを読み込むことが可能
	ch2 := make(chan int, 2)
	ch2 <- 1
	ch2 <- 2
	close(ch2) // チャネルを閉じる

	// バッファ1のデータ受信
	v, ok = <-ch2
	fmt.Printf("%v %v\n", v, ok) // 1 true

	// バッファ2のデータ受信
	v, ok = <-ch2
	fmt.Printf("%v %v\n", v, ok) // 2 true

	// 取り出すバッファがなくなったデータ受信
	v, ok = <-ch2
	fmt.Printf("%v %v\n", v, ok) // 0 false
}
```

## capsel

チャネルの作成と書き込みを関数内に閉じ込める（カプセル化）

```go
package main

import "fmt"

// int型の読み取り専用チャネルを返す関数（チャネルの作成と書き込みを関数内にカプセル化）
func generateCountStream() <-chan int {
	ch := make(chan int)
	go func() {
		defer close(ch)
		for i := 0; i <= 5; i++ {
			ch <- i
		}
	}()
	return ch
}

func main() {
	ch := generateCountStream()

	// for rangeの場合<-は省略可
	for v := range ch {
		fmt.Println(v)
	}
}
```

## notification

チャネルのデータ受信を開始するとデータが受信されるまで以後のコード実行を待機する。その特徴を活かして待機したい場所に通知用チャネルを用いるテクニックがある。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	nCh := make(chan struct{}) // 通知専用の型は0byteのstructの空の定義が適切
	var wg sync.WaitGroup
	for i := 0; i <= 5; i++ {
		wg.Add(1)
		go func(i int) {
			defer wg.Done()
			fmt.Printf("goroutine %v started\n", i)
			<-nCh          // 通知用チャネルのデータ受信を開始し、以後のコードを待機する
			fmt.Println(i) // チャネル解放後実行される
		}(i)
	}

	// 2秒間の待機の後、通知用チャネルをcloseすると待機していたチャネルが解放されて処理が続行される
	time.Sleep(2 * time.Second)
	close(nCh)
	fmt.Println("unblocked by manual close")

	wg.Wait()
	fmt.Println("finish")
}
```
