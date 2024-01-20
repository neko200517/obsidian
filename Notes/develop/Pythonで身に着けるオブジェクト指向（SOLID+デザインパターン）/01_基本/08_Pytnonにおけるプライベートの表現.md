Pythonでは慣例としてプライベートな属性・メソッドには名前の先頭に_をつけて表現する

```python
# Pythonでは慣例としてプライベートな属性・メソッドには名前の先頭に_をつけて表現する

class MyClass:
  def __int__(self, value: str) -> None:
    self._value = value

  def _private_method(self) -> None:
    print("private method")

  def public_method(self) -> None:
    print("public method")

my_class = MyClass(4)

# ただし実際にはアクセス可能
print(my_class._value)
print(my_class._private_method())
```

一応Pythonにはアクセス制限する機能として

- 名前マングリング（name mangling）
- @propertyデコレータ

といった機能が提供されている。
ただし完全にプライベートな属性・メソッドを定義できない