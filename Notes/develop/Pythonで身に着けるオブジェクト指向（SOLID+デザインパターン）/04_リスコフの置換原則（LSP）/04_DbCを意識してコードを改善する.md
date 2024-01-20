## 契約による設計が行われていないコード

```python
# 契約による設計を意識していないコード
class BankAccount:
    def __init__(self) -> None:
        self._balance = 0

    def deposit(self, amount: int) -> None:
        self._balance += amount

    def withdraw(self, amount: int) -> None:
        self._balance -= amount
```

上記のコードの問題点
- 入出金額を負の値にできてしまう
- 残高が0より小さくなる可能性がある
- 残高をクラスの外部から変更できる

これらの問題は銀行口座が持つ以下のようなルールがコードに反映されていないために起こる
- 入出金額は0以上の整数値（事前条件）
- 残高以上の額を出金することはできない（事前条件）
- 入金・出金額と口座残高が整合性を保つ必要がある（事後条件）
- 残高は常に0以上の整数値（不変条件）

## 事前条件を満たすようにコードを改善する

事前条件を満たすために入金額が0以上の確認、引き出し額が残高以上の額になっていないかを確認する。

```python
# 事前条件を守れるように変更したコード（ガード節を追加）
class BankAccount:
    def __init__(self) -> None:
        self._balance = 0

    # 入金
    def deposit(self, amount: int) -> None:
        if amount < 0:
            raise ValueError("事前条件：入金額は0以上")
        self._balance += amount

    # 出金
    def withdraw(self, amount: int) -> None:
        if amount < 0:
            raise ValueError("事前条件：出金額は0以上")
        if amount > self._balance:
            raise ValueError("事前条件：引き出し額は現在の残高以下")
        self._balance -= amount
```

契約に違反した場合は例外を発生させることで契約に違反したまま処理が続くことを妨げる。

## 事後条件を満たすようにコードを改善する

事後条件を満たすため、処理前と処理後の整合性をチェックする。

```python
# 事後条件を守れるように変更したコード
class BankAccount:
    def __init__(self) -> None:
        self._balance = 0

    # 入金
    def deposit(self, amount: int) -> None:
        if amount < 0:
            raise ValueError("事前条件：入金額は0以上")

        old_balance = self._balance
        new_balance = self._balance + amount
        self._check_postcondition(new_balance, old_balance, amount)
        self._balance = new_balance

    # 出金
    def withdraw(self, amount: int) -> None:
        if amount < 0:
            raise ValueError("事前条件：出金額は0以上")
        if amount > self._balance:
            raise ValueError("事前条件：引き出し額は現在の残高以下")

        old_balance = self._balance
        new_balance = self._balance - amount
        self._check_postcondition(new_balance, old_balance, amount)
        self._balance = new_balance

    # 事後条件を確認するメソッド
    def _check_postcondition(self, new_balance: int, old_balance: int, amount: int):
        diff = new_balance - old_balance
        if abs(diff) != amount:
            raise ValueError("事後条件：入金・出金額と口座残高に整合性がある")
```

実用の場面において事後条件は事前条件や不変条件のようにガード節で組み込むというよりかは、テストケースとして確認されるのが基本となる。

⇒　冗長なので事後条件が組み込まれるケースはほとんどない
　⇒　テストする際のテストコードとして用いる

## 不変条件を満たすようにコードを改善する

不変条件を満たすため、新しく代入する残高が0以上の整数値であることを確認する。

```python
# 不変条件を守れるように変更したコード
class BankAccount:
    def __init__(self) -> None:
        self._balance = 0

    # クラス外部から変更されないようにする
    @property
    def balance(self):
        return self._balance

    # 入金
    def deposit(self, amount: int) -> None:
        if amount < 0:
            raise ValueError("事前条件：入金額は0以上")

        new_balance = self._balance + amount
        self._check_invariant(new_balance)  # 不変条件をチェック
        self._balance = new_balance

    # 出金
    def withdraw(self, amount: int) -> None:
        if amount < 0:
            raise ValueError("事前条件：出金額は0以上")
        if amount > self._balance:
            raise ValueError("事前条件：引き出し額は現在の残高以下")

        new_balance = self._balance - amount
        self._check_invariant(new_balance)  # 不変条件をチェック
        self._balance = new_balance

    # 不変条件を確認するメソッド
    def _check_invariant(self, new_balance):
        if new_balance < 0:
            raise ValueError("不変条件：残高は常に0以上の整数値")
```

契約による設計を意識するとコードがより正確・頑健になる。
