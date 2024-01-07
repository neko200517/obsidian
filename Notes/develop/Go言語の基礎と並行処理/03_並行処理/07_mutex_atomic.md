## mutex

ゴルーチンを同時に実行したときデータ競合が起こることがある。これを回避するために排他制御のMutexを利用する。

```go
package main

// 排他制御

import (
	"fmt"
	"sync"
)

func main() {
	var wg sync.WaitGroup
	var mu sync.Mutex
	var i int
	wg.Add(2)
	go func() {
		defer wg.Done()
		mu.Lock()         // 排他制御を行いたい場所に追加
		defer mu.Unlock() // defer: 排他制御をアンロック
		i++
	}()
	go func() {
		defer wg.Done()
		mu.Lock()         // 排他制御を行いたい場所に追加
		defer mu.Unlock() // defer: 排他制御をアンロック
		i++
	}()
	wg.Wait()
	fmt.Println(i)
}
```

--raceオプションを追加することでデータ競合を検出可能

```bash
go run --race main.go
```

## RWMutex

複数のゴルーチンを同時実行する際に、書き込みのロックはLockを使用し、並列可能なものに対してはRLockを使うなどして使い分ける。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// RLockは並列処理中もコードのロックが行われない
func read(mu *sync.RWMutex, wg *sync.WaitGroup, c *int) {
	defer wg.Done()
	time.Sleep(10 * time.Millisecond)
	mu.RLock() // ロック中は並列処理が可能。ただし値の書き込みに使用しないこと推奨
	defer mu.RUnlock()

	fmt.Println("read lock")
	fmt.Println(*c)
	time.Sleep(1 * time.Second)
	fmt.Println("read unlock")
}

// Lockはロックが解放されるまで並列処理はできない
func write(mu *sync.RWMutex, wg *sync.WaitGroup, c *int) {
	defer wg.Done()
	mu.Lock() // ロック中は並列処理は出来ない
	defer mu.Unlock()

	fmt.Println("write lock")
	*c += 1
	time.Sleep(1 * time.Second)
	fmt.Println("write unlock")
}

func main() {
	var wg sync.WaitGroup
	var rwMu sync.RWMutex
	var c int

	wg.Add(4)
	go write(&rwMu, &wg, &c)
	go read(&rwMu, &wg, &c)
	go read(&rwMu, &wg, &c)
	go read(&rwMu, &wg, &c)

	wg.Wait()
	fmt.Println("finish")
}
```

## atomic

atomicパッケージは自前で排他制御を組まなくても簡単に行える関数が多数用意されている。

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	var wg sync.WaitGroup
	var c int64

	for i := 0; i < 5; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			for j := 0; j < 10; j++ {
				atomic.AddInt64(&c, 1)
			}
		}()
	}
	wg.Wait()
	fmt.Println(c)
	fmt.Println("finish")
}
```
