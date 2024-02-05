依存関係をモジュールの外部から注入するパターン
依存関係を注入することで動的に依存関係を変更することができる

```python
from abc import ABC, abstractmethod


class AbstractNotification(ABC):  # 抽象クラス
    @abstractmethod
    def send(send, user_id: int) -> None:
        pass


class EmailNotification(AbstractNotification):  # 具象クラス
    def send(send, user_id: int) -> None:
        print("Email")


class SMSNotification(AbstractNotification):  # 具象クラス
    def send(send, user_id: int) -> None:
        print("SMS")


# どの具象クラスに依存するかは定義時にはまだ決まっていない
def notify(user_id: int, notification: AbstractNotification):
    notification.send(user_id)


notification1 = EmailNotification()
notification2 = SMSNotification()

# 関数の呼び出し時に具象クラスへの依存を注入する
notify(1, notification1)
notify(2, notification2)
```

モジュールの定義時には抽象に依存しておいて（DIP）、実行時に具象への依存をモジュールに注入する（DI）ことで依存関係を動的に決定することができる
⇒　OCPが実現できる（ただしLSPは前提）

## どこから依存性を注入するのか

依存性を注入する方法としては
- コンストラクタ
- メソッドの引数
などがあるが、コンストラクタから注入されることが多い