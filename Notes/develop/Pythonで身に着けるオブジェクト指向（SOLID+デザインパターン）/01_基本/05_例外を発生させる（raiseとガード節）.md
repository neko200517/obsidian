## 例

20文字以上の名前を入力したら例外が発生する。

```python
class User:
  def __init__(self, name):
    if len(name) > 20:
      raise ValueError("ユーザー名が20文字を超えています")
    self.name = name

try:
  user = User(input("ユーザー名を入力してください："))
except ValueError as e:
  print(e)
```

## ガード節

不正な値が渡されたら例外が発生させるようなメソッドの先頭の記述をガード節という。