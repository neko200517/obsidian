## VSCodeのセットアップ

### 拡張機能のインストール

以下の拡張機能を導入する。

- Python
- Black Formatter

### フォーマッタの設定

Ctrl + Shift + P でVSCodeの設定ファイルを開き、次の設定を追加する。

```json
// settings.json
{
  // 省略
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter",
    "editor.formatOnSave": true
  },
  "python.formatting.provider": "none",
}
```

## 必要なライブラリのインストール

### venvで仮想環境を作成

```shell
python -m venv fastapi
```

### 仮想環境をアクティブにする

```shell
# Windowsの場合
./fastapi/Scripts/activate
```

シェルの先頭に (fastapi) と表示されていればOK

### ライブラリをインストールする

```shell
# fastapiのインストール
pip install fastapi 

# uvicornのインストール
pip install uvicorn
```

###

### Hello World を書いてみる

main.pyというファイルを作成し以下のように記述する

```python
# main.py 
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def index():
    return {"message": "Hello World"}
```

## FastAPIの起動方法

```shell
uvicorn main:app --reload
```

## APIドキュメントの自動生成

### Swagger UI

自動でAPIドキュメントが生成される機能が組み込まれているため、以下のURLを入力することで簡単にドキュメントを閲覧可能。

#### docs
http://localhost:8000/docs 

#### redoc
http://localhost:8000/redoc

## パスパラメータ

{param} と定義することでクエリパラメータを指定できる。関数には引数として値を取得することができる。

```python
# main.py

# 省略
@app.get("/countries/{country_name}")
async def contry(country_name: str):
    return {"country_name": country_name}
```

例えば http://localhost:8000/country_name/japan と入力すると{"country_name": "japan"} といった json が出力される。

また引数に型ヒントを付与することでバリデーションチェックを行える。

## クエリパラメータ 

以下のように記述することでクエリパラメータを実装できる。

```python
# main.py 

# 省略
@app.get("/countries/")
async def contry(country_name: str = "japan", country_no: int = 1):
    return {"country_name": country_name, "country_no": country_no}
```

## 必須ではないオプションパラメータ

typingをimportし、Optionsl[型] = Noneとすることでデフォルト引数をNoneとして扱うことができる。

```python
# main.py 
from typing import Optional

# 省略 
@app.get("/hobbies/")
async def hobby(hobby_name: Optional[str] = None, hobby_no: Optional[int] = None):
    return {"hobby_name": hobby_name, "hobby_no": hobby_no}
```

## リクエストボディ

リクエストボディをPOSTメソッドで呼び出す例。BaseModelを継承したモデルを定義して型を推論可能にしている。

```python
# main.py
from pydantic import BaseModel

# 省略 
class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: int
    tax: Optional[float] = None

@app.post("/item/")
async def create_item(item: Item):
    return {"message": f"{item.name}は税込価格{int(item.price * item.tax)}円です。"}
```

http://localhost/docs で表示された関数の「Try it out」→「Execute」を実行すると簡易テスト可能。

## PythonでPostリクエストを送る

テストならPostmanなど専用のテストツールを使うのも良いが、Pythonでもリクエストを投げることができる。

```python
import requests
import json

def main(url: str, body: dict):
    # Point: jsonを文字列型に変換する
    res = requests.post(url, json.dumps(body))
    print(res.json())

if __name__ == "__main__":
    url = "http://localhost:8000/item/"
    body = {"name": "item", "description": "hoge", "price": 100, "tax": 1.1}
    main(url, body)
```

## 入れ子構造になっているリクエストボディ 

以下のような入れ子構造を持つjsonがあったとする。

```json
{
  "shop_info": {
    "name": "sample shop",
    "location": "Tokyo"
  },
  "items": [
    {
      "name": "item1",
      "desctiption": "item1 detail",
      "price": 100,
      "tax": 1.1
    },
    {
      "name": "item2",
      "desctiption": "item2 detail",
      "price": 200,
      "tax": 1.1
    }
  ]
}
```

以下のように型を定義してあげたら簡単に実装可能。

```python
from fastapi import FastAPI
from typing import Optional, List
from pydantic import BaseModel

app = FastAPI()

# 型
class ShopInfo(BaseModel):
    name: str
    location: str

class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: int
    tax: Optional[float] = None

class Data(BaseModel):
    shop_info: Optional[ShopInfo]
    items: List[Item]

@app.post("/")
async def index(data: Data):
    return {"data": data}
```

==Python3.9以上はtypingは非推奨となっている。Optionalは不要で、Listはlistで代替可能==

### バリデーションチェック 

Fieldを使うことでバリデーションチェックを自動化できる。
以下の例はnameは4文字以上、12以下でないとエラーになり、priceは0以上でないとエラーになる。

```python
from pydantic import BaseModel, Field

# 省略
class Item(BaseModel):
    name: str = Field(min_length=4, max_length=12)
    description: Optional[str] = None
    price: int = Field(ge=0)
    tax: Optional[float] = None 
```