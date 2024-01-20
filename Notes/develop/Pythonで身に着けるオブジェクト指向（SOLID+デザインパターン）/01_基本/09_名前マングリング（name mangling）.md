## name mangling

```python
# 先頭に2個以上のアンダースコア、末尾に1個以下のアンダースコアで表現する
#   ⇒Pythonが内部的に「_クラス名__変数名」という形式に変換している
# __init__や__add__などは末尾に2つのアンダースコアがあるのでname manglingが行われない
# 「変数名_」という変数名はPythonの予約語を回避する用途で使用される(listやdictなど)

class Circle:
  # プライベートな定数
  __PI = 3.14

  def __init__(self, r):
    self.__r = r

  def length(self):
    return self.__r * 2 * Circle.__PI

  def area(self):
    return self.__r * self.__r * Circle.__PI

# print(Circle.__PI) # 外部から呼び出せない

circle = Circle(5)
# print(circle.__r) # 外部から呼び出せない

print(circle._Circle__PI) # 「_クラス名__属性名」とすれば呼び出し可能
print(circle._Circle__r) # 「_クラス名__属性名」とすれば呼び出し可能
```

name manglingを使ってもアクセス可能なのでプライベートなアクセス制限をしたい場合は「＿変数名」で表現する方が好まれる。

name manglingは親クラスと子クラスで属性の名前が衝突したことで親クラスの属性を子クラスに上書きしてしまうことを避ける用途で使われる。