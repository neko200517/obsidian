@propertyデコレータを使用することでGetterメソッドのような使い方ができ、ReadOnlyなアクセサを定義することができる。

```python
class Rectangle:
  def __init__(self, width: int, height: int) -> None:
    self._width = width
    self._height = height

  # widthのGetterメソッド
  @property
  def width(self) -> int:
    return self._width

  # heightのGetterメソッド
  @property
  def height(self) -> int:
    return self._height

  # クラス内部からは変更可能
  def change_value(self, width, height) -> None:
    self._width = width
    self._height = height

rect = Rectangle(1, 2)
# rect.width = 10 # Getterは外部から変更不可
print(rect.width)
```