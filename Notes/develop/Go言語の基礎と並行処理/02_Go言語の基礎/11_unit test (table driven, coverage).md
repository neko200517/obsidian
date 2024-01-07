## unit test (table driven)

以下のサンプルコードを記述
### コードを作成

```go
package main

import "fmt"

func Add(x, y int) int {
	return x + y
}

func Divide(x, y int) float32 {
	if y == 0 {
		return 0.
	}
	return float32(x) / float32(y)
}

func main() {
	x, y := 3, 5
	fmt.Printf("%v %v\n", Add(x, y), Divide(x, y)) // 8 0.6
}
```

### テストコードの生成

Add, Divideの位置で右クリック→「Go: Generate Unit Tests For Function」を選択
以下のコードが生成されるので、テストケースを追加

```go
// main_test.go

package main

import (
	"testing"
)

func TestAdd(t *testing.T) {
	type args struct {
		x int
		y int
	}
	tests := []struct {
		name string
		args args
		want int
	}{
		// ↓ 追加
		{
			name: "1+2=3",          // テスト名
			args: args{x: 1, y: 2}, // 引数
			want: 3,                // 評価する値
		},
		{
			name: "2+3=5",
			args: args{x: 2, y: 3},
			want: 5,
		},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			if got := Add(tt.args.x, tt.args.y); got != tt.want {
				t.Errorf("Add() = %v, want %v", got, tt.want)
			}
		})
	}
}

func TestDivide(t *testing.T) {
	type args struct {
		x int
		y int
	}
	tests := []struct {
		name string
		args args
		want float32
	}{
		// ↓ 追加
		{
			name: "1/2=0.5",
			args: args{x: 1, y: 2},
			want: 0.5,
		},
		{
			name: "2/0=0.0",
			args: args{x: 2, y: 0},
			want: 0,
		},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			if got := Divide(tt.args.x, tt.args.y); got != tt.want {
				t.Errorf("Divide() = %v, want %v", got, tt.want)
			}
		})
	}
}
```

### テスト実行

```bash
go test -v .
```

## coverage

### カバレッジを計測

先に不要なコードを削除（コメント化）しておく

```go
package main

func Add(x, y int) int {
	return x + y
}

func Divide(x, y int) float32 {
	if y == 0 {
		return 0.
	}
	return float32(x) / float32(y)
}

func main() {
	// x, y := 3, 5
	// fmt.Printf("%v %v\n", Add(x, y), Divide(x, y)) // 8 0.6
}
```

その状態で以下のコマンドを実行
coverageに結果が出力される

```go
go test -v -cover -coverprofile=coverage .
```

### テスト不足の出力

```bash
go tool cover -html=coverage
```
