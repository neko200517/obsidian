## 置換できないのはどのような場合か

次のいずれかが起こると基本型を派生型で置換できなくなる

1. 派生型で基本型よりも事前条件を強める 
2. 派生型で基本型よりも事後条件を弱める 
3. 派生型で基本型の不変条件に違反する
4. 派生型で基本型にはない例外が発生する（1と同じ）

## 事前条件を派生型で強めている

### 問題のあるパターン１

```python
# 事前条件を派生型で強めているパターン
class SuperHoge:
    def hoge(self, n: int) -> int:
        return n


class SubHoge(SuperHoge):
    def hoge(self, n: int) -> int:
        if n < 0:
            raise ValueError("0以上にしてください")
        return n
```

「派生型で基本型にない例外が発生している」
派生型で基本型の事前条件を強めていることになるので基本型と派生型を置換不可能にする。

### 問題のあるパターン２

だからといってガード節をあえて書かないと要件が満たせなくなる。

```python
# 事前条件を派生型で強めているパターン(例外の発生なし)
class SuperHoge:
    def hoge(self, n: int) -> int:
        return n


class SubHoge(SuperHoge):
    def hoge(self, n: int) -> int:
        # nは0以上を期待しているがガード節がない
        return n
```

### 問題のないパターン 

```python
# 事前条件を派生型で弱めているパターン
class SuperHoge:
    def hoge(self, n: int) -> int:
	    if n < 0:
		    raise ValueError("0以上にしてください")
        return n


class SubHoge(SuperHoge):
    def hoge(self, n: int) -> int:
        return n


def hoge0(hoge_obj: SuperHoge):
	print(hoge_obj.hoge(0))
```

基本型が正常動作する呼び出し方を派生型でも同様に行って問題ないなら置換可能。

## 派生型で事後条件を弱めるケース

### 問題のあるパターン

```python
# 事後条件を派生型で弱めているパターン
class SuperHoge:
    def hoge(self, n: int) -> int:
        return n


class SubHooge(SuperHoge):
    def hoge(self, n: int) -> int | tuple[int, str]:  # 戻り値の範囲が広くなっている
        if n > 0:
            return n, "正の数です"
        return n


def hoge_client(hoge_obj: SuperHoge):
    return hoge_obj(2) * 10
```

派生型の戻り値が複数の型で返っている。
hoge_clientでint型かtuple型どちらが返る変わらないため良くないパターン。

### 問題のないパターン 

```python
# 事後条件を派生型で強めているパターン
class SuperHoge:
    def hoge(self, n: int) -> int:
        return n


class SubHooge(SuperHoge):
    def hoge(self, n: int) -> int:
        if n < 0:
            return 0  # 負の数はすべて0にして返す。戻り値の繁栄が狭くなっている。
        return n
```

## 不変条件を派生型で違反するケース 

```python
# 不変条件に派生型で違反しているパターン
class SuperHoge:
    def __init__(self, n: int) -> None:
        if n < 1:
            raise ValueError("1以上にしてください")  # 不変条件、nは1以上
        self._n = n

    def hoge(self, n: int) -> int:
        # 事前条件
        if n < 1:
            raise ValueError("1以上にしてください")
        self._n = self._n * n
        return self._n


class SubHonge(SuperHoge):
    def hoge(self, n: int) -> int:
        # 事前条件
        if n < 1:
            raise ValueError("1以上にしてください")
        # // ... 切り捨て除算
        self._n = self._n // n  # nが1よりも小さくなる可能性がある
        return self._n
```

ガード節でnを1以上の制約を設けているが、派生型で_nを1よりも小さい値にする処理を入れているため、派生型で不変条件を違反している。