## Settings.py

rest_api/settings.pyに設定を追加する。

```python
# rest_api/settngs.py

# 省略

INSTALLED_APPS = [
	# 省略
	'rest_framework',
	'rest_framework.authtoken',
	'corsheaders',
	'api.apps.ApiConfig',
]

MIDDLEWARE = [
	# 省略
	'corsheaders.middleware.CorsMiddleware',
]

# 実行を許可するurlを追加
CORS_ORIGIN_WHITELIST = [
	'http://localhost:3000'
]

# RestFrameworkの設定を追加
REST_FRAMEWORK = {
  'DEFAULT_PERMISSION_CLASSES': [
    'rest_framework.permissions.IsAuthenticated',
  ],
  'DEFAULT_AUTHENTICATION_CLASSES': [
    'rest_framework.authentication.TokenAuthentication',
  ]
}

# 省略
```

# models.py

api/models.pyを編集

```python
# api/models.py

# djangoFrameworkのmodelsをインポート
from django.db import models
# djangoFrameworkのUserをインポート
from django.contrib.auth.models import User

# Segmentクラスを定義
class Segment(models.Model):
  # 文字列型(最大100文字)
  segment_name = models.CharField(max_length=100)

  # print(Segmengインスタンス)などの使い方でsegment_nameを返すようにオーバーロード
  def __str__(self):
    return self.segment_name


class Brand(models.Model):
  brand_name = models.CharField(max_length=100)

  def __str__(self):
    return self.brand_name


class Vehicle(models.Model):
  # 外部キーの定義(オブジェクト、削除されたら自動でVehicleを削除)
  user = models.ForeignKey(
    User,
    on_delete=models.CASCADE
  )
  vehicle_name = models.CharField(max_length=100)
  # 整数型
  release_year = models.IntegerField()
  # Decimal型(少数部を含めた最大桁数、少数第２位)
  price = models.DecimalField(max_digits=6, decimal_places=2)
  segment = models.ForeignKey(
    Segment,
    on_delete=models.CASCADE
  )
  brand = models.ForeignKey(
    Brand,
    on_delete=models.CASCADE
  )
  
  def __str__(self):
    return self.vehicle_name
```

## マイグレーション

models.pyに定義したSegment, Brand, Vehicleをマイグレーションする。
デフォルトでdb.sqlite3に保存される。

```sh
python manage.py makemigrations
python manage.py migrate
```

## admin.py

ダッシュボードに作成したテーブルを追加するため、admin.pyにモデルを追加

```python
# api/admin.py 
from django.contrib import admin
from .models import Segment, Brand, Vehicle

admin.site.register(Segment)
admin.site.register(Brand)
admin.site.register(Vehicle)
```

## スーパーユーザーの作成

Djangoのダッシュボードにログインするためスーパーユーザーを作成する。

```sh
python manage.py createsuperuser
# username 
# email
# password
```

## 管理用ダッシュボードにアクセス

開発用サーバを立ち上げ```http://127.0.0.1:8000/admin/```にアクセス。

```sh
python manage.py runserver
```

## サンプル

### Brand

- band name: Tesla

### Segment 

- segment name: Sedan

### Vehicle 

- User: super
- Vehicle name: MODEL S
- Release year: 2020
- Price: 300.12
- Segment: Sedan 
- Brand: Tesla 
