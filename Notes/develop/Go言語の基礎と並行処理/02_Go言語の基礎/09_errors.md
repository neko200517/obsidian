## errors

エラーの生成、比較、ラップ、センチネルエラーなど

```go
package main

import (
	"errors"
	"fmt"
	"os"
)

var ErrCustom = errors.New("not found")

// ラップされたerrorを返すファイルチェッカー
func fileChecker(name string) error {
	f, err := os.Open(name)
	if err != nil {
		return fmt.Errorf("in checker: %w", err)
	}
	defer f.Close()
	return nil
}

func main() {
	err01 := errors.New("something wrong")
	fmt.Printf("%[1]p %[1]T %[1]v\n", err01) // メモリアドレス *errors.errorString something wrong
	fmt.Println(err01.Error())               // something wrong
	fmt.Println(err01)                       // Printlnはerror型のメソッドを引数に持ち、内部的にError()を呼び出すため省略可

	// 比較
	err02 := errors.New("something wrong") // err01と同じ文字列のerror
	fmt.Println(err01 == err02)            // false

	// Errorのラップ
	// fmt.ErrorfでErrorを拡張する。%wで作成された場合*fmt.wrapErrorの型となる
	//Unwrapすることでerror.Newで作成されたオリジナルの構造体の取得が可能
	err0 := fmt.Errorf("add info: %w", errors.New("original error"))
	fmt.Printf("%[1]p %[1]T %[1]v\n", err0) // メモリアドレス *fmt.wrapError add info: original error
	fmt.Println(errors.Unwrap(err0))        // original error
	fmt.Printf("%T\n", errors.Unwrap(err0)) // *errors.errorString

	// %vで作成すると*errors.errorString型となる
	// errors.Unwrapにはerrors.errorStringの実態はないためnilを返す仕様となっている
	err1 := fmt.Errorf("add info: %v", errors.New("original error"))
	fmt.Printf("%[1]p %[1]T %[1]v\n", err1) // メモリアドレス *errors.errorString add info: original error
	fmt.Println(err1)                       // add info: original error
	fmt.Printf("%T\n", err1)                // *errors.errorString
	fmt.Println(errors.Unwrap(err1))        // nil

	// センチネルエラー（エラーの多層化）
	err2 := fmt.Errorf("in repository layer: %w", ErrCustom) // 1階層目
	fmt.Println(err2)                                        // in repository layer: not found
	err2 = fmt.Errorf("in service layer: %w", err2)          // 2階層目
	fmt.Println(err2)                                        // in service layer: in repository layer: not found

	// センチネルエラーの比較（いずれかの階層にマッチしたらtrue）
	if errors.Is(err2, ErrCustom) {
		fmt.Println("matched")
	}

	// ファイルの存在を確認する
	// エラーがある場合：
	//    存在しない場合：not found
	//    それ以外の場合：unknown error
	file := "dummy.txt"
	err3 := fileChecker(file)
	if err3 != nil {
		if errors.Is(err3, os.ErrNotExist) {
			fmt.Printf("%v file not found\n", file)
		} else {
			fmt.Println("unknown error")
		}
	}
}
```

## 資料

https://cs.opensource.google/go/go/+/master:src/errors/errors.go