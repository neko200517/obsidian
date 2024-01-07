## selectの注意点

```go
package main

// 補足：チャネルを同時に受信してselectで評価する場合、受信したチャネルの処理はランダムに選択される

import "fmt"

func main() {
	ch1 := make(chan string, 1)
	ch2 := make(chan string, 1)

	ch1 <- "ch1"
	ch2 <- "ch2"

	select {
	case v := <-ch1:
		fmt.Println(v)
	case v := <-ch2:
		fmt.Println(v)
	}
}
```
