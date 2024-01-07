## 仮想環境の構築

```sh
python -m venv venv
venv/Scripts/activate
```

## Djangoのインストール

```sh
pip install Django 
pip install djangorestframework
pip install django-cors-headers
```

## Djangoプロジェクトの作成

```sh
django-admin startproject rest_api .
django-admin startapp api
```

## 開発用サーバの起動

```
python manage.py runserver
```
