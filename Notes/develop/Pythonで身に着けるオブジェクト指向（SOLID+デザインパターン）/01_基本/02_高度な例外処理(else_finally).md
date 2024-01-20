## else finaly

```python
if __name__ == "__main__":
  i = 0
  while True:
    try:
      n = int(input("整数値を入力してください:"))
    except ValueError:
      print("入力エラー：整数値を入力してください")
    else: # tryの中で例外が発生した場合の処理
      print("正常な値が入力されました")
      print(n)
      break
    finally: # 例外の発生の有無によらず実行される処理
      i += 1

print(f"{i}回目の入力で成功しました")
```

## ユースケース

- finally
	- データベースの切断などの後処理
- else:
	- 例外が発生しない場合に、後処理の前に行いたい処理を記述する
		- ⇒try句の記述を最小限にできる
- try句に書く処理は最小にするのがポイント
	- ⇒どの行で例外が発生したのかをわかりやすくするため