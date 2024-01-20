## デザインパターン

23種類のGoFデザインパターンが有名

## Facadeパターン

複雑なオブジェクト処理の手順をまとめて簡単なインタフェースを提供するパターン 

```python
# 在庫管理クラス
class Inventory:
  def check(self) -> None:
    print("在庫を確認します")

  def reduce(self) -> None:
    print("在庫を減らします")

# 支払処理クラス
class Payment:
  def process(self) -> None:
    print("支払いを処理します")

# 配送処理クラス
class Shipping:
  def ship_order(self) -> None:
    print("注文を出荷します")

# 注文処理のFacade
class OrderFacade:
  def __init__(self) -> None:
    self.inventory = Inventory()
    self.payment = Payment()
    self.shipping = Shipping()

  def place_order(self) -> None:
    self.inventory.check()
    self.inventory.reduce()
    self.payment.process()
    self.shipping.ship_order()

order_facade = OrderFacade()
order_facade.place_order()
```

OrderFacade クラスを用意することでInventory クラス・Payment クラス・Shipping クラスを具体的にどう組み合わせるかを呼び出し側で意識する必要がなくなる。

その結果として
- コード量が少なくなる
- メソッドの呼び出し順を間違えなくなる
- 呼び出し順など注文の手続きが変わってもFacadeの中身を変えるだけで良い

SRPを守るために分割されたクラスはFacadeでまとめてシンプルなインタフェースを提供するようにすると良い。