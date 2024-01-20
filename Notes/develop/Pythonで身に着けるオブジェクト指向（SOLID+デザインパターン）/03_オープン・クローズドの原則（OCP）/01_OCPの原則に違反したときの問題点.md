既存のコードを変更することなく機能（パターン）を追加するべきであるという原則。

## Bad

```python
class Notification:
  def send(self, notification_type: str, user_id: int):
    if notification_type == "email":
      print("メール")
    elif notification_type == "sms":
      print("SMS")
    elif notification_type == "push":
      print("プッシュ通知")

notification = Notification()
notification.send("sms", 123)
notification.send("push", 456)
```

Notification クラスはOCPに違反しているので通知方法が増えた場合にsendメソッドの変更が必要になる。
- 通知パターンが増えるたびにsendメソッドが肥大化する
- 既存機能を書き換えてバグが起こる可能性が出てくる

⇒　sendメソッドを変更することなく通知のパターンを追加できるようにする必要がある

## Good

```python
from abc import ABC, abstractmethod

# 通知のインタフェース
class AbstractNotification(ABC):
  # 継承したクラスは必ずこのメソッドをオーバーライドする必要がある
  @abstractmethod
  def send(self, user_id: id) -> None:
    pass

# 具体的な実装
class EmailNotification(AbstractNotification):
  def send(self, user_id: int) -> None:
    print("メール")

class SMSNotification(AbstractNotification):
  def send(self, user_id: int) -> None:
    print("SMS")

class PushNotification(AbstractNotification):
  def send(self, user_id: int) -> None:
    print("Push")

email_notification = EmailNotification()
email_notification.send(123)

sms_notification = SMSNotification()
sms_notification.send(456)

push_notification = PushNotification()
push_notification.send(678)
```

OCPの原則に則ったコードを設計することで使用の追加に伴う
- コードの肥大化の問題
- 既存のコードに変更を変更していしまう可能性

すべてを解消することができる。

## SRPとの違い

抽象クラスを使うことによって
- 具象クラスに共通のインタフェースを定義することができる
- 通知機能のクライアントが具象クラスに依存しなくなる

⇒　具象クラスを切り替えることができるようになる

```python
from abc import ABC, abstractmethod

# 通知のインタフェース
class AbstractNotification(ABC):
  # 継承したクラスは必ずこのメソッドをオーバーライドする必要がある
  @abstractmethod
  def send(self, user_id: id) -> None:
    pass

# 具体的な実装
class EmailNotification(AbstractNotification):
  def send(self, user_id: int) -> None:
    print("メール")

class SMSNotification(AbstractNotification):
  def send(self, user_id: int) -> None:
    print("SMS")

class PushNotification(AbstractNotification):
  def send(self, user_id: int) -> None:
    print("Push")

# 通知クライアント
def notify(user_id: int, notification: AbstractNotification):
  notification.send(user_id)

# 使用例
notifications = {
  "email": EmailNotification(),
  "sms": SMSNotification(),
  "push": PushNotification(),
}

notification_type = input("通知の種類を入力:")
notify(1, notifications[notification_type])
```

抽象クラスを受け取るクライアント関数を作成することで変更に強いコードになる。