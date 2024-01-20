## ISPの違反例

```python
# ドキュメントの値オブジェクト
class Document:
    def __init__(self) -> None:
        pass


# 複合機クラス
class MultifunctionPrinter:
    def print_document(self, document: Document) -> None:
        print("プリントを開始します")

    def copy_document(self, document: Document) -> None:
        print("コピーを開始します")


# クライアント
class PrintClient:
    def __init__(self, printer: MultifunctionPrinter) -> None:
        self.printer = printer

    def execute(self, document: Document) -> None:
        self.printer.print_document(document)


class CopyClient:
    def __init__(self, printer: MultifunctionPrinter) -> None:
        self.printer = printer

    def execute(self, document: Document) -> None:
        self.printer.copy_document(document)
```

上記の複合機クラスはクライアント側から見て不要なメソッドを公開しているのでISPに違反している。

## ISPの改善例

多重継承を使ったISPの改善例
クライアントごとに必要なメソッドのみを公開したインタフェースを利用する

```python
# インタフェース分離の一例
from abc import ABC, abstractmethod


# ドキュメントの値オブジェクト
class Document:
    def __init__(self) -> None:
        pass


# プリント機能のみを提供するインタフェース
class PrinterInterface(ABC):
    @abstractmethod
    def print_document(self, document: Document) -> None:
        pass


# コピー機能のみを提供するインタフェース
class CopyInterface(ABC):
    @abstractmethod
    def copy_document(self, document: Document) -> None:
        pass


# 複合機クラス
# プリント機能インタフェースとコピー機能インタフェースを多重継承
class MultifunctionPrinter(PrinterInterface, CopyInterface):
    def print_document(self, document: Document) -> None:
        print("プリントを開始します")

    def copy_document(self, document: Document) -> None:
        print("コピーを開始します")


# クライアント
class PrintClient:
    def __init__(self, printer: PrinterInterface) -> None:
        self.printer = printer

    def execute(self, document: Document) -> None:
        self.printer.print_document(document)  # 不要なメソッドがない


class CopyClient:
    def __init__(self, copier: CopyInterface) -> None:
        self.copier = copier

    def execute(self, document: Document) -> None:
        self.copier.copy_document(document)  # 不要なメソッドがない
```