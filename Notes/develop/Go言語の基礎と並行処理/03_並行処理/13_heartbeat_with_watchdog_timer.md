## 監視プログラム 

一定間隔のハートビートで値の取得を繰り返す監視プログラム。
エラーが起こるとログファイルにエラー内容と時刻を書き込む。

```go
package main

// 一定間隔のハートビートで値の取得を繰り返す監視プログラム

import (
	"context"
	"fmt"
	"io"
	"log"
	"os"
	"strings"
	"time"
)

// タスク関数
// 引数：
//
//	第一引数：キャンセル用コンテキスト
//	第二引数：ハートビートのインターバル
//
// 戻り値：
//
//	heartbeat：ハートビートのチャネル
//	out：出力チャネル
func task(
	ctx context.Context,
	beatInterval time.Duration,
) (<-chan struct{}, <-chan time.Time) {
	heartbeat := make(chan struct{})
	out := make(chan time.Time)
	go func() {
		defer close(heartbeat)
		defer close(out)
		pulse := time.NewTicker(beatInterval)    // 500ms
		task := time.NewTicker(2 * beatInterval) // 1000ms

		// ハートビートのチャネルに書き込む無名関数
		sendPulse := func() {
			select {
			case heartbeat <- struct{}{}: // 受信を開始したらデータを書き込み送信
			default: // それ以外
			}
		}

		// 出力チャネルに書き込む関数
		sendValue := func(t time.Time) {
			for {
				select {
				case <-ctx.Done():
					return
				case <-pulse.C:
					sendPulse() // 500ms間隔でハートビートのチャネルに書き込み
				case out <- t:
					return // 出力チャネルに時刻を書き込んでループを抜ける
				}
			}
		}

		// ハートビートのチャネルと出力チャネルに値を書き込み
		var i int
		for {
			select {
			case <-ctx.Done():
				return
			case <-pulse.C:
				//** カウントが3になったら意図的にタイムアウトする **//
				if i == 3 {
					time.Sleep(1000 * time.Millisecond)
				}
				//***********************************************//
				sendPulse() // 500msでハートビートのチャネルに書き込み
				i++
			case t := <-task.C:
				sendValue(t) // 1000ms間隔で出力チャネルに書き込み
			}
		}
	}()

	// ハートビート、出力チャネルを返す
	return heartbeat, out
}

func main() {
	file, err := os.Create("log.txt")
	if err != nil {
		log.Fatalln(err)
	}
	defer file.Close()

	// エラーが発生した場合にログ出力
	// 　第一引数：標準出力とログファイルに出力
	// 　第二引数：ERROR: を先頭に付与
	// 　第三引数：エラーの発生時刻を付与
	errorLogger := log.New(io.MultiWriter(file, os.Stderr), "ERROR: ", log.LstdFlags)

	// コンテキストの作成（5100msでタイムアウト）
	ctx, cancel := context.WithTimeout(context.Background(), 5100*time.Millisecond)
	defer cancel()

	// ウォッチドックのタイムアウト（800ms）
	const wdtTimeout = 800 * time.Millisecond
	// ハートビートの間隔（500ms）
	const beatInterval = 500 * time.Millisecond

	// タスク関数の呼び出し
	heartbeat, v := task(ctx, beatInterval)

	// ウォッチドックの実装
loop:
	for {
		select {
		case _, ok := <-heartbeat: // ハートビートのチャネルを受信した場合
			if !ok {
				break loop
			}
			fmt.Println("beat pulse ⚡")
		case r, ok := <-v: // 出力チャネルを受信した場合
			if !ok {
				break loop
			}
			t := strings.Split(r.String(), "m=") // モノトニック時刻（経過時間）だけ取得
			fmt.Printf("value: %v [s]\n", t[1])
		case <-time.After(wdtTimeout): // ハートビート、出力チャネルいずれも受信せずに800msが経過した場合
			errorLogger.Println("doTask goroutin`s heartbeat stopped")
			break loop
		}
	}
}
```
