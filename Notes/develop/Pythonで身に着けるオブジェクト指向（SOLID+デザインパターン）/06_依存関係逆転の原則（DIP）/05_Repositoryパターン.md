## DIPの主な適用場所

データベースアクセスや外部サービスの利用などの技術的な詳細を扱う場合には基本的にDIPを適用する

ユーザーの新規作成を例にしてデータベースアクセスを行う例

## 良くない例

```python
# 最悪の方法（SQLをコードにべた書き）
class User:
    def __init__(self, name: str) -> None:
        self.name = name


class UserApplicationService:
    # ユーザーの新規作成
    def register_user(self, name: str) -> None:
        user = User(name)

        # 中略：データベースに接続

        query = f"INSERT INTO users (name) VALUES ('{user.name}')"

        # 中略：データベースの開放


# 使用例
user_application_service = UserApplicationService()
user_application_service.register_user("User")
```

次のコードには以下の問題点がある
- register_userメソッドの責務はユーザーの登録だけでなくデータベース操作の責務も持ってしまっている
- データベースが変わったときにregister_userメソッドを変更する必要がある
- データベースと密結合しているためregister_userメソッドのユニットテストが難しい

## 良い例（まだ改善の余地あり）

```python
# クラスを適用した方法
class User:
    def __init__(self, name: str) -> None:
        self.name = name


# Repositoryパターン
class SQLiteUserRepository:
    def add(self, user: User) -> None:
        # 中略：データベースに接続
        query = f"INSERT INTO users (name) VALUES ('{user.name}')"
        # 中略：データベースの開放


# ユーザーのユースケースを表現するクラス
class UserApplicationService:
    def __init__(self) -> None:
        self.repository = SQLiteUserRepository()

    def register_user(self, name: str) -> None:
        user = User(name)
        self.repository.add(user)


# 使用例
user_application_service = UserApplicationService()
user_application_service.register_user("User")
```

改善点
- データベース操作をSQLiteUserRepositoryクラスで抽象化
- その結果、データベース操作の責務がSQLiteUserRepository クラスに分割された

これはドメイン駆動設計のRepositoryパターンと呼ばれる設計パターンとなる
⇒　データベース操作用のクラスを用意することでデータベース操作の詳細を隠ぺいする