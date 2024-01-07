## slice

動的に追加可能な配列。appendによる追加、makeによる宣言、レンジを使った切り出し、copyによるスライスのクローンなど。
※切り出しによるメモリの共有の挙動に注意

```go
package main

// スライス：後から要素を追加可能

import "fmt"

func main() {
	// スライスの宣言
	var s1 []int
	s2 := []int{}

	fmt.Printf("s1: %[1]T %[1]v %v %v\n", s1, len(s1), cap(s1)) // s1: []int [] 0 0
	fmt.Printf("s2: %[1]T %[1]v %v %v\n", s2, len(s2), cap(s2)) // s2: []int [] 0 0

	// 宣言の仕方で初期化された値が異なる
	fmt.Println(s1 == nil) // true
	fmt.Println(s2 == nil) // false

	// 要素の追加
	s1 = append(s1, 1, 2, 3)
	fmt.Printf("s1: %[1]T %[1]v %v %v\n", s1, len(s1), cap(s1)) // s1: []int [1 2 3] 3 3

	// スライスに別のスライスを追加
	s3 := []int{4, 5, 6}
	s1 = append(s1, s3...)                                      // ...を付与することでs3を展開してappendに渡す
	fmt.Printf("s1: %[1]T %[1]v %v %v\n", s1, len(s1), cap(s1)) // s1: []int [1 2 3 4 5 6] 6 6

	// makeを使ったスライスの宣言
	s4 := make([]int, 0, 2)                                     // 要素数0, キャパシティ2
	fmt.Printf("s4: %[1]T %[1]v %v %v\n", s4, len(s4), cap(s4)) // s4: []int [] 0 2

	// makeで作ったスライスに値を追加(キャパシティを超える場合自動的にキャパシティを増やす)
	s4 = append(s4, 1, 2, 3, 4)
	fmt.Printf("s4: %[1]T %[1]v %v %v\n", s4, len(s4), cap(s4)) // s4: []int [1 2 3 4] 4 4

	// makeを使ったスライスの宣言
	s5 := make([]int, 4, 6)                                     // 要素数4, キャパシティ6, 0が埋められて初期化
	fmt.Printf("s5: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5)) // s5: []int [0 0 0 0] 4 6

	// s5[1], s5[2]の要素をレンジで取り出して新たにs6を宣言
	s6 := s5[1:3]                                               // レンジの最後はindex+1を指定する
	s6[1] = 10                                                  // s6[1]はs5[2]の参照なのでs[2]の要素も10に変更される
	fmt.Printf("s5: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5)) // s5: []int [0 0 10 0] 4 6
	fmt.Printf("s6: %[1]T %[1]v %v %v\n", s6, len(s6), cap(s6)) // s6: []int [0 10] 2 5
	s6 = append(s6, 2)                                          // s6[1]のアドレスがs5[2]と共有したためにs6に要素を追加した場合でもs5[3]の要素まで書き換わってしまう
	fmt.Printf("s5: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5)) // s5: []int [0 0 10 2] 4 6
	fmt.Printf("s6: %[1]T %[1]v %v %v\n", s6, len(s6), cap(s6)) // s6: []int [0 10 2] 3 5

	// 参照元まで変更されるのを回避するためにはcopyを使ってスライスをコピーする
	sc6 := make([]int, len(s5[1:3]))
	fmt.Printf("s5 source of copy: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5))      // s5 : []int [0 0 10 2] 4 6
	fmt.Printf("sc6 dst copy before: %[1]T %[1]v %v %v\n", sc6, len(sc6), cap(sc6)) // sc6: []int [0 0] 2 2
	copy(sc6, s5[1:3])
	fmt.Printf("sc6 dst copy after: %[1]T %[1]v %v %v\n", sc6, len(sc6), cap(sc6)) // sc6: []int [0 10] 2 2
	sc6[1] = 12
	fmt.Printf("s5: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5))     // s5 : []int [0 0 10 2] 4 6 → アドレスが共有されていないことを確認
	fmt.Printf("sc6: %[1]T %[1]v %v %v\n", sc6, len(sc6), cap(sc6)) // sc6: []int [0 12] 2 2

	// 部分的な共有
	s5 = make([]int, 4, 6)
	fs6 := s5[1:3:3]                                                // s5[1], s5[2]までメモリを共有 [最初のindex:切り出す最後のindex+1:共有する範囲index-1]
	fmt.Printf("s5: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5))     // s5 : []int [0 0 0 0] 4 6
	fmt.Printf("fs6: %[1]T %[1]v %v %v\n", fs6, len(fs6), cap(fs6)) // sc6: []int [0 0] 2 2
	fs6[0] = 6
	fs6[1] = 7
	fs6 = append(fs6, 8)
	fmt.Printf("s5: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5))     // s5 : []int [0 6 7 0] 4 6 → s5[3]と共有していないことを確認
	fmt.Printf("fs6: %[1]T %[1]v %v %v\n", fs6, len(fs6), cap(fs6)) // sc6: []int [6 7 8] 3 4
	s5[0] = 1
	s5[1] = 2
	s5[2] = 3
	s5[3] = 4
	fmt.Printf("s5: %[1]T %[1]v %v %v\n", s5, len(s5), cap(s5))     // s5 : []int [1 2 3 4] 4 6 → s5の変更がfs6に影響を与えていない。fs6 → s5のみ影響を受けることを確認
	fmt.Printf("fs6: %[1]T %[1]v %v %v\n", fs6, len(fs6), cap(fs6)) // sc6: []int [6 7 8] 3 4
}
```

## map

mapの追加、取得、削除、for文など

```go
package main

import "fmt"

func main() {
	// mapの宣言
	var m1 map[string]int // map[keyの型]valueの型
	m2 := map[string]int{}

	fmt.Printf("m1: %v %v\n", m1, m1 == nil) // map[] true
	fmt.Printf("m2: %v %v\n", m2, m2 == nil) // map[] false

	// 要素の追加、取得
	m2["A"] = 10
	m2["B"] = 20
	m2["C"] = 0
	fmt.Printf("m2: %v %v %v\n", m2, len(m2), m2["A"]) // map[A:10 B:20 C:0] 3 10

	// 要素の削除
	delete(m2, "A")
	fmt.Printf("m2: %v %v %v\n", m2, len(m2), m2["A"]) // map[B:20 C:0] 2 0 ← 存在しないものを参照すると0が返却される

	// 存在の確認
	v, ok := m2["A"]             // 2番目の戻り値に存在のbool値が返ってくる仕様になっている
	fmt.Printf("%v %v\n", v, ok) // 0 false

	v, ok = m2["C"]
	fmt.Printf("%v %v\n", v, ok) // 0 true

	// mapを使ったfor文
	// ※mapの各要素は内部的にハッシュによって格納されているため必ず追加順に取得できるとは限らない点に注意
	for k, v := range m2 { // 1番目：key, 2番目：value
		fmt.Printf("%v %v\n", k, v)
	}
}
```
