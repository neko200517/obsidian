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

## Brand のテスト

```python
from django.contrib.auth.models import User
from django.urls import reverse
from django.test import TestCase
from rest_framework import status
from rest_framework.test import APIClient
from .models import Brand
from .serializers import BrandSerializer

BRAND_URL = "/api/brands/"


# テスト用セグメントの作成
def create_brands(brand_name):
    return Brand.objects.create(brand_name=brand_name)


# URL/idを含めたURLを取得する
def detail_url(brand_id):
    return reverse("api:brand-detail", args=[brand_id])


# セグメントのテスト（認証あり）
class AuthorizedBrandApiTests(TestCase):
    def setUp(self):
        username = "testuser"
        password = "testuser"
        self.user = User.objects.create_user(username=username, password=password)
        self.client = APIClient()
        self.client.force_authenticate(self.user)

    # APIにGETをリクエストして取得したすべてのデータがDBに登録されたデータと一致していること
    def test_3_01_should_get_all_brand(self):
        create_brands(brand_name="Toyota")
        create_brands(brand_name="Honda")
        res = self.client.get(BRAND_URL)

        # Brandのデータを全て取得し辞書型に変換
        brands = Brand.objects.all().order_by("id")
        serializer = BrandSerializer(brands, many=True)  # 複数ある場合はmany=Trueにする

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # リクエストして取得した全てのデータがDBに登録されたデータと一致していること
        self.assertEqual(res.json(), serializer.data)

    # APIにGETをリクエストして取得した単独のデータがDBに登録されたデータと一致していること
    def test_3_02_should_get_single_brand(self):
        brand = create_brands(brand_name="Toyota")
        url = detail_url(brand.pk)
        res = self.client.get(url)

        # Brandのデータを単独で取得し辞書型に変換
        brands = Brand.objects.get(id=brand.pk)
        serializer = BrandSerializer(brands)

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # リクエストして取得した全てのデータがDBに登録されたデータと一致していること
        self.assertEqual(res.json(), serializer.data)

    # APIにPOSTをリクエストするとセグメントが新規作成されること
    def test_3_03_should_create_new_brand_successfully(self):
        payload = {"brand_name": "Toyota"}
        res = self.client.post(BRAND_URL, payload)

        # APIを実行したらスタータスコード201が返却されること
        self.assertEqual(res.status_code, status.HTTP_201_CREATED)

        # 作成したセグメントがDBに存在することを確認すること
        exists = Brand.objects.filter(brand_name=payload["brand_name"]).exists()
        self.assertTrue(exists)

    # APIにセグメント名が空の状態でPOSTを実行するとセグメントが新規作成されないこと
    def test_3_04_should_not_create_new_brand_with_invalid(self):
        payload = {"brand_name": ""}
        res = self.client.post(BRAND_URL, payload)

        # APIを実行したらスタータスコード400が返却されること
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # APIにPATCHを実行するとセグメントが部分的に更新されること
    def test_3_05_should_partial_update_brand(self):
        brand = create_brands(brand_name="Toyota")
        url = detail_url(brand.pk)

        # データを部分的に更新する
        payload = {"brand_name": "Honda"}
        res = self.client.patch(url, payload)
        brand.refresh_from_db()  # DBを更新

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # 更新したセグメント名が更新内容と一致すること
        self.assertEqual(brand.brand_name, payload["brand_name"])

    # APIにPUTを実行するとセグメントが更新されること
    def test_3_06_should_update_brand(self):
        brand = create_brands(brand_name="Toyota")
        url = detail_url(brand.pk)

        # データを更新する
        payload = {"brand_name": "Honda"}
        res = self.client.put(url, payload)
        brand.refresh_from_db()  # DBを更新

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # 更新したセグメント名が更新内容と一致すること
        self.assertEqual(brand.brand_name, payload["brand_name"])

    # APIにPUTを実行するとセグメントが削除されること
    def test_3_07_should_delete_brand(self):
        brand = create_brands(brand_name="Toyota")

        # 作成したセグメントの数量が1であること
        self.assertEqual(1, Brand.objects.count())
        url = detail_url(brand.pk)

        # データを削除する(refreshは不要)
        res = self.client.delete(url)

        # APIを実行したらスタータスコード204が返却されること
        self.assertEqual(res.status_code, status.HTTP_204_NO_CONTENT)

        # 削除したセグメントの数量が1であること
        self.assertEqual(0, Brand.objects.count())


# セグメントのテスト（認証なし）
class UnauthorizedBrandApiTests(TestCase):
    def setUp(self):
        self.client = APIClient()

    # 認証されていないユーザーがAPIにGETをリクエストすると401が返却されること
    def test_3_08_should_get_brand(self):
        res = self.client.get(BRAND_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにPOSTをリクエストすると401が返却されること
    def test_3_09_should_create_brand(self):
        res = self.client.post(BRAND_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにPATCHをリクエストすると401が返却されること
    def test_3_10_should_patch_brand(self):
        res = self.client.patch(BRAND_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにPUTをリクエストすると401が返却されること
    def test_3_11_should_put_brand(self):
        res = self.client.put(BRAND_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにDELETEをリクエストすると401が返却されること
    def test_3_12_should_delete_brand(self):
        res = self.client.delete(BRAND_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

```
