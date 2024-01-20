## コンポジション

実装を別のクラスで定義し、呼び出し側で処理を呼び出す

```python
# コンポジション
from abc import ABC, abstractmethod


# 共通のインタフェース
class Cource(ABC):
    @abstractmethod
    def view_lecture(self) -> None:
        pass


# 講師用の具象クラス
class InstructorBehavior:
    @abstractmethod
    def add_lecture(self, countent_name: str) -> None:
        print(f"新規レクチャー「{countent_name}」を作成しました")


# 生徒用の具象クラス
class StudentBehavior:
    @abstractmethod
    def review_cource(self) -> None:
        print("コースのレビューをお願いします")


# 講師クラス
class CourseFromInstructors(Cource):
    def __init__(self) -> None:
        self.instructor_behavior = InstructorBehavior()  # 具象クラスをコンポジション

    def view_lecture(self) -> None:
        print("視聴を開始します")

    def add_lecture(self, countent_name: str) -> None:
        self.instructor_behavior.add_lecture(countent_name)  # 具象クラスの呼び出し


# 生徒クラス
class CourseFromStudents(Cource):
    def __init__(self) -> None:
        self.student_behavior = StudentBehavior()  # 具象クラスをコンポジション

    def view_lecture(self) -> None:
        print("視聴を開始します")

    def review_cource(self) -> None:
        self.student_behavior.review_cource()  # 具象クラスの呼び出し
```