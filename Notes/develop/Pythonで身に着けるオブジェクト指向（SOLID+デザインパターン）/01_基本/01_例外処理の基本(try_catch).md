## try catch

```python
if __name__ == "__main__":
  while True:
    try:
      n = int(input("整数値を入力してください:"))
      print(n)
      break
    except ValueError:
      print("入力エラー：整数値を入力してください")
```
