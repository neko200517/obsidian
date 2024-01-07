## ユーザー認証している場合のテスト

api/test_1_user.pyを作成し、テストコードを書いていく。
ファイル名は必ずtest_xxxxxのような形でtestが接頭語に来ていること。

```python
# api/test_1_user.py

from django.test import TestCase
from django.contrib.auth.models import User
from rest_framework.test import APIClient
from rest_framework import status


CREATE_USER_URL = "/api/create/"
PROFILE_URL = "/api/profile/"
TOKEN_URL = "/api/auth/"


# ユーザー認証のテスト
class AuthorizedUserApiTests(TestCase):
    # 初期設定
    def setUp(self):
        self.username = "testuser"
        self.password = "testuser"
        self.user = User.objects.create_user(
            username=self.username, password=self.password
        )
        self.client = APIClient()

        # 強制的に認証を通す
        self.client.force_authenticate(user=self.user)

    # ユーザープロフィールのテスト
    def test_1_1_should_get_user_profile(self):
        # ダミーユーザーでgetメソッドでprofile_urlにアクセス
        res = self.client.get(PROFILE_URL)

        # responseのstatusCodeが200と一致しているかを確認
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # responseで取得したデータのid, usernameが一致しているかを確認
        data = res.json()
        self.assertEqual(data["id"], self.user.pk)
        self.assertEqual(data["username"], self.user.username)

    # PUTのテスト(許可しない)
    def test_1_2_should_not_allowed_by_PUT(self):
        payload = {"usename": self.user.username, "password": self.user.password}

        # putメソッドでアクセス
        res = self.client.put(PROFILE_URL, payload)

        # StatusCodeが405になっていること
        self.assertEqual(res.status_code, status.HTTP_405_METHOD_NOT_ALLOWED)

    # PATCHのテスト(許可しない)
    def test_1_3_should_not_allowed_by_PATCH(self):
        payload = {"usename": self.user.username, "password": self.user.password}

        # patchメソッドでアクセス
        res = self.client.patch(PROFILE_URL, payload)

        # StatusCodeが405になっていること
        self.assertEqual(res.status_code, status.HTTP_405_METHOD_NOT_ALLOWED)

```