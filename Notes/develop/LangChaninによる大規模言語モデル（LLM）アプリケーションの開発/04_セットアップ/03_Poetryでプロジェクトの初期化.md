## Poetryの初期化

```bash
poetry init
```

### pyproject.tomlを一部変更

```toml
[tool.poetry]
name = "langchain-001"
version = "0.1.0"
description = ""
authors = ["Jun Terayama <pam130901@gmail.com>"]

[tool.poetry.dependencies]
python = "3.11.3" # ←バージョンを指定

[tool.poetry.dev-dependencies]

[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"
```

## Poetryをローカルのみに適用する

```bash
poetry config virtualenvs.in-project true --local
```

## Poetryで実行するpythonの場所を指定 

```bash
poetry env use /home/ubuntu/.asdf/shims/python
```

## 確認

```bash
poetry run python --version
```

## インストールされているバージョンの一覧表示

```bash
poetry env list
```

## 特定のバージョンを削除

```bash
poetry env remove {バージョンを指定}
```

## Pythonコマンドの実行例

```bash
poetry run python src/hello.py
```