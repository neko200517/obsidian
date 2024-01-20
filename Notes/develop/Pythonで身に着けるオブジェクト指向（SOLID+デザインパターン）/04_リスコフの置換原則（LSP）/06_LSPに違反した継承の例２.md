```python
# ②次のコードはLSPに違反しているか
# adjust_width関数から見たときに、LSPに違反しているか？

class Rectangle:  # 長方形クラス
    def __init__(self, width: int, height: int) -> None:
        self.width = width
        self.height = height

    def area(self) -> int:
        return self.width * self.height


class Square(Rectangle):  # 正方形クラス
    def __init__(self, side: int) -> None:
        super().__init__(side, side)


def adjust_width(rectangle: Rectangle, new_width: int) -> None:
    print(f"初期の面積：{rectangle.area()}")
    rectangle.width = new_width
    print(f"幅を調整後の面積：{rectangle.area()}")


rectangle = Rectangle(5, 10)
adjust_width(rectangle, 7)

square = Square(5)
adjust_width(square, 7)  # 5 * 7 = 35: 期待した結果と異なる
```

以上のコードはLSPに違反している。
adjust_widthでwidthを変更しているが、heightと一致していないため正方形の面積としては結果が異なっているためである。

「正方形 is a 長方形（正方形は長方形の一つ）」なのは間違いないが、用語が「is a」の関係だからと言って適切な継承ができているとは限らない。

⇒　クライアントから見て「is a」の関係が成立している必要がある

⇒　LSPに違反しているかどうか曖昧なクラスの関係は、クライアントから見てLSPを満たしているか？が適切な継承の判断基準となる

## ただし

次のようなクライアントから見れば上記の基本型と派生型はLSPを満たしていることになる

```python
def print_area(rectanble: Rectangle) -> None:
    print(f"面積：{rectangle.area()}")


rectangle = Rectangle(5, 10)
print_area(rectangle) # 50

square = Square(5)
print_area(square) # 25
```