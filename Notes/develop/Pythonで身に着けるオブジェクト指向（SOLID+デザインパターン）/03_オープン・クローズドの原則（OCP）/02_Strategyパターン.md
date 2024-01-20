アルゴリズムを実行時に選択できるようになるパターンのこと

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