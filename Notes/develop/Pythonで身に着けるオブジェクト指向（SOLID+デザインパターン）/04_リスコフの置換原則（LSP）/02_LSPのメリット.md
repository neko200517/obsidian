LSPを満たして派生型が基本型と置換可能になるとサブタイプを切り替えられるようになる。

⇒　OCPを実現できる継承が可能になる
　⇒　OCPを実現するための前提となっているのがLSP
　　⇒　インプットとアウトプットが同じ形式であることを原則とする

```python
# Strategyパターン
from abc import ABC, abstractmethod

# ストラテジーのインタフェース
class AbstractStrategy(ABC):
  @abstractmethod
  def do(self):
    pass

# 具体的なストラテジー
class StrategyA(AbstractStrategy):
  def do(self):
    print("StrategyA")

class StrategyB(AbstractStrategy):
  def do(self):
    print("StrategyB")

class StrategyC(AbstractStrategy):
  def do(self):
    print("StrategyC")

# クライアント
def client(strategy: AbstractStrategy):
  strategy.do()
```

継承はもともとコードの再利用による保守性の向上を目的として誕生したが、実際には保守性を低下させるような使われ方が非常に多かった（※神クラスなど）

⇒　最近のプログラミング言語ではクラスの継承を禁止している言語もある（Go/Rustなど）
⇒　LSPを守るように継承を利用すればOCPを守れるようになり変更に強くなる

※神クラス：一つのクラスに余りにも多くの機能を装備してしまい、コードが肥大化した様子を示すアンチパターン