## ビューの作成

ビューにアクセスした際に処理を実行

```python
# api/views.py

from rest_framework import generics, permissions, viewsets, status
from rest_framework.response import Response
from .serializers import UserSerializer, SegmentSerializer, BrandSerializer, VehicleSerializer
from .models import Segment, Brand, Vehicle


# ユーザー作成
# generics.CreateAPIView・・・登録（POST）
class CreateUserView(generics.CreateAPIView):
  # UserSerializerを割り当て
  serializer_class = UserSerializer
  # 認証なしでもアクセス可能にする
  permission_classes = (permissions.AllowAny,)


# ログインしているユーザーのユーザー情報を返す
# generics.RetrieveUpdateAPIView・・・取得（GET, PUT, PATCH）
class ProfileUserView(generics.RetrieveUpdateAPIView):
  serializer_class = UserSerializer

  # ログインユーザーを返す
  def get_object(self):
    return self.request.user
  
  # PUTで呼び出し場合
  def update(self, request, *args, **kwargs):
    # 後で編集可能にするが今はエラーを返す
    respose = {"mesage": "PUT method is not allowed"}
    return Response(respose, status=status.HTTP_405_METHOD_NOT_ALLOWED)
  
  # PATCHで呼び出した場合
  def partial_update(self, request, *args, **kwargs):
    # 後で編集可能にするが今はエラーを返す
    respose = {"mesage": "PATCH method is not allowed"}
    return Response(respose, status=status.HTTP_405_METHOD_NOT_ALLOWED)


# SegmentのCRUD操作を行う
class SegmentViewSet(viewsets.ModelViewSet):
  queryset = Segment.objects.all()
  serializer_class = SegmentSerializer


# BrandのCRUD操作を行う
class BrandViewSet(viewsets.ModelViewSet):
  queryset = Brand.objects.all()
  serializer_class = BrandSerializer


# VehicleのCRUD操作を行う
class VehicleViewSet(viewsets.ModelViewSet):
  queryset = Vehicle.objects.all()
  serializer_class = VehicleSerializer

  # Vehicleを新規作成する
  def perform_create(self, serializer):
    # user属性に現在ログイン中のユーザーを割り当て
    serializer.save(user=self.request.user)
```

## 補足：Generic View クラス一覧

### 一覧

|   |   |
|---|---|
|CreateAPIView|登録（POST）|
|ListAPIView|一覧取得（GET）|
|RetrieveAPIView|取得（GET）|
|UpdateAPIView|更新（PUT、PATCH）|
|DestroyAPIView|削除（DELETE）|

### 組み合わせ

**ListCreateAPIView** （POST、GET） = ListAPIView + CreateAPIView  
**RetrieveUpdateAPIView**（GET、PUT、PATCH） = RetrieveAPIView + UpdateAPIView  
**RetrieveDestroyAPIView**（GET、DELETE） = RetrieveAPIView + RetrieveDestroyAPIView  
**RetrieveUpdateDestroyAPIView**（GET、PUT、PATCH） = RetrieveAPIView + UpdateAPIView + DestroyAPIView

## URLにビューを紐づけ

api/urls.pyを新規作成し、先ほど作ったビューとURLを関連付けていく。
api/urls.py・・・アプリケーションレベルのurls.py

```python
# api/urls.py

from django.urls import path, include
from rest_framework.authtoken.views import obtain_auth_token
from .import views
from rest_framework.routers import DefaultRouter

# Router:ビューとURLを紐づけ
router = DefaultRouter()
router.register("segments", views.SegmentViewSet)
router.register("brands", views.BrandViewSet)
router.register("vechicles", views.VehicleViewSet)

app_name = "api"

# エンドポイントの登録
urlpatterns = [
  # genericsのviewはas_viewでビューにキャスト
  path("create/", views.CreateUserView.as_view(), name="create"),
  path("profile/", views.ProfileUserView.as_view(), name="profile"),
  # トークン取得用エンドポイント
  path("auth/", obtain_auth_token, name="auth"),
  # ルートにアクセスがあった場合、登録したRouterを参照する
  path("", include(router.urls)),
]
```

プロジェクトレベルのurls.pyに作成したアプリケーションレベルのurls.pyを追加してアクセス可能にする。
rest_api/urls.py・・・プロジェクトレベルのurls.py

```python
# rest_api/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path("api/", include("api.urls"))
]
```
