OCPと関連性の高いデザインパターンにDecoratorパターンがある

⇒　既存のオブジェクトを変更せずにオブジェクトに機能を追加することを可能にするパターン 

```python
# よくない設計

# ベースのコーヒークラス
class Coffee:
  @property
  def cost(self) -> int:
    return 200

  @property
  def description(self) -> str:
    return "コーヒー"

# 生クリームをトッピングしたコーヒー
class CreamCofee(Coffee):
  @property
  def cost(self) -> int:
    return super().cost + 50

  @property
  def description(self) -> str:
    return f"{super().description}、生クリーム"

# バニラアイスをトッピングしたコーヒー
class VanillaCofee(Coffee):
  @property
  def cost(self) -> int:
    return super().cost + 70

  @property
  def description(self) -> str:
    return f"{super().description}、バニラアイス"

cream_cofee = CreamCofee()
print(cream_cofee.cost)
print(cream_cofee.description)

vanilla_cofee = VanillaCofee()
print(vanilla_cofee.cost)
print(vanilla_cofee.description)
```

問題点 
- トッピングの種類が増えるたびにクラスを用意しなければならない
- 例えば生クリームとバニラを組み合わせたクラスなども用意しなければならない

⇒　コードの量が膨大になる

### Decoratorパターンを

以下のようにシンプル＋複雑な実装が実現可能

```python
cofee = Cofee()

cofee_with_cream = CreamDecorator(cofee)
cofee_with_vanilla = VanillaDecorator(cofee)
cofee_with_cream_and_vanilla = VanillaDecorator(cofee_with_cream)
```