---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - python
  - django
  - rest-api
  - test
aliases:
  - <% tp.file.title %>
---

## 仮想環境の構築

```sh
python -m venv venv
venv/Scripts/activate
```

## Django のインストール

```sh
pip install Django
pip install djangorestframework
pip install django-cors-headers
```

## Django プロジェクトの作成

```sh
django-admin startproject rest_api .
django-admin startapp api
```

## 開発用サーバの起動

```
python manage.py runserver
```
