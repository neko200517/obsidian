クライアントにクライアントが利用しないメソッドへの依存を強制してはならない

## ISPに違反したコード

```python
from abc import ABC, abstractmethod


class Cource(ABC):
    # 共通のメソッド
    @abstractmethod
    def view_lecture(self) -> None:
        pass

    # 更新専用のメソッド
    @abstractmethod
    def add_lecture(self, countent_name: str) -> None:
        pass

    # 生徒専用のメソッド
    @abstractmethod
    def review_cource(self) -> None:
        pass


# 講師クラス
class CourseFromInstructors(Cource):
    def view_lecture(self) -> None:
        print("視聴を開始します")

    def add_lecture(self, countent_name: str) -> None:
        print(f"新規レクチャー「{countent_name}」を作成しました")

    def review_cource(self) -> None:
        pass  # 講師はレビューできないので実装しない


# 生徒クラス
class CourseFromStudents(Cource):
    def view_lecture(self) -> None:
        print("視聴を開始します")

    def add_lecture(self, countent_name: str) -> None:
        pass  # 生徒はレクチャーを追加できないので実装しない

    def review_cource(self) -> None:
        print("コースのレビューをお願いします")
```

上記のコードでは
講師と生徒のぞれぞれしか使わないメソッドが抽象クラスで定義されているため具象クラスが使わないメソッドに依存することを強制していしまっている。
- 講師にとってはreview_courseメソッドが不要
- 生徒にとってはadd_lectureメソッドが不要 
⇒　ISPに違反している

ISPに違反すると次の問題が発生する可能性が高い
- 不要な依存関係が生まれるのでクラス間の結合度が高まる
- インタフェース（抽象クラス）が大きくなりSRPに違反する
- サブクラスでメソッドを退化させる（事後条件を弱める）ことになりLSPに違反する
⇒　コードの変更がしづらくなる