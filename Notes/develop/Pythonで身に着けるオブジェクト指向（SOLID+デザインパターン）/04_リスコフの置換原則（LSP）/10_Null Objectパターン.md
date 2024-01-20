## Null Object パターンを実装してない例

クーポンがない場合の実装例：
⇒　デフォルト値としてNoneを設定してクーポンが存在しなければそのままの金額を出力する
⇒　できるだけ判定とNoneを追い出したい

```python
# 中略

class ShoppingCart:
    def __init__(self, discount_strategy: CouponStrategy = None) -> None:
        self.items = []
        self.discount_storategy = discount_strategy

    def add_item(self, item_name: str, price: int):
        self.items.append((item_name, price))

    def calculate_total(self) -> int:
        context = DiscountParameters(self.items)
        # クーポンの使用があるかどうかで条件分岐
        if self.discount_storategy is not None:
            return self.discount_storategy.apply_discount(context)
        return context.total_price

cart = ShoppingCart()
cart.add_item("item", 100)
print(f"{cart.calculate_total()}円")
```

## Null Object パターンを実装した例

Null Objectパターンを使うことでNoneチェックの条件分岐をなくして他のクーポンと同様に扱えるようにする

```python
# 中略

# Null Object（他の派生型と同じインタフェースを持ったNoneの表現）
class NoCoupopn(CouponStrategy):
    def apply_discount(self, context: DiscountParameters) -> int:
        return context.total_price


class ShoppingCart:
    def __init__(self, discount_strategy: CouponStrategy) -> None:
        self.items = []
        self.discount_storategy = discount_strategy

    def add_item(self, item_name: str, price: int):
        self.items.append((item_name, price))

    def calculate_total(self) -> int:
        context = DiscountParameters(self.items)
        return self.discount_storategy.apply_discount(context)


cart = ShoppingCart(NoCoupopn())  # クーポンなしの場合
cart.add_item("item", 100)
print(f"{cart.calculate_total()}円")
```
