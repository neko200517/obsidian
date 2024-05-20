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

## ユーザー認証していない場合のテスト

```python
# api/test_1_user.py

中略

# ユーザー認証のテスト（認証なし）
class UnAuthorizedUserApiTests(TestCase):
    def setUp(self):
        self.client = APIClient()

    # ユーザー作成ができること
    def test_1_4_should_create_new_user(self):
        payload = {"username": "test", "password": "testuser"}

        # ユーザーの作成が成功するとステータスコード201が返却されること
        res = self.client.post(CREATE_USER_URL, payload)
        self.assertEqual(res.status_code, status.HTTP_201_CREATED)

        # 作成されたユーザーを取得
        user = User.objects.get(**res.json())

        # リクエストしたパスワードと作成されたユーザーのパスワードが一致すること
        self.assertTrue(user.check_password(payload["password"]))

        # レスポンスの中にpasswordが含まれていないこと
        self.assertNotIn("password", res.json())

    # 同じユーザー名を登録しようとするとエラーが返却されること
    def test_1_5_should_not_create_user_by_same_credentials(self):
        payload = {"username": "test", "password": "testuser"}

        # ユーザーを作成
        User.objects.create_user(**payload)

        # 同じ名前のユーザーを作成しようとするとステータスコード400が返却されること
        res = self.client.post(CREATE_USER_URL, payload)
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # パスワードの入力が5文字より少ないときエラーになること
    def test_1_6_should_not_create_user_with_short_pw(self):
        payload = {"username": "test", "password": "test"}
        res = self.client.post(CREATE_USER_URL, payload)
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # トークンが取得できること
    def test_1_7_should_response_token(self):
        payload = {"username": "test", "password": "testuser"}
        User.objects.create_user(**payload)

        # トークン取得URLにアクセス
        res = self.client.post(TOKEN_URL, payload)

        # 返却されたjsonにトークン属性が含まれていること
        self.assertIn("token", res.json())

        # スタータスコード200が返却されていること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

    # アクセスしたユーザーのパスワードが異なっていた場合、トークン取得が失敗すること
    def test_1_8_should_not_response_token_with_invalid_credentials(self):
        payload = {"username": "test", "password": "testuser"}
        User.objects.create_user(**payload)

        # 間違ったパスワードでアクセス
        bad_payload = {"username": "test", "password": "bad_testuser"}
        res = self.client.post(TOKEN_URL, bad_payload)

        # 返却されたjsonにトークン属性が含まれていないこと
        self.assertNotIn("token", res.json())

        # ステータスコード400が返却されていること
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # トークン取得で存在しないユーザーをアクセスしようとしたときにエラーになること
    def test_1_9_should_not_response_token_with_non_exist_crendential(self):
        # 存在しないユーザーでアクセス
        bad_payload = {"username": "test", "password": "testuser"}
        res = self.client.post(TOKEN_URL, bad_payload)

        # 返却されたjsonにトークン属性が含まれていないこと
        self.assertNotIn("token", res.json())

        # ステータスコード400が返却されていること
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # トークン取得でパスワードが空のリクエストでアクセスしようとしたときにエラーになること
    def test_1_10_should_not_response_token_with_non_exist_crendential(self):
        # パスワードが空でアクセス
        bad_payload = {"username": "test", "password": ""}
        res = self.client.post(TOKEN_URL, bad_payload)

        # 返却されたjsonにトークン属性が含まれていないこと
        self.assertNotIn("token", res.json())

        # ステータスコード400が返却されていること
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # トークン取得でユーザー名とパスワードが空のリクエストでアクセスしようとしたときにエラーになること
    def test_1_11_should_not_response_token_with_non_exist_crendential(self):
        # パスワードが空でアクセス
        bad_payload = {"username": "", "password": ""}
        res = self.client.post(TOKEN_URL, bad_payload)

        # 返却されたjsonにトークン属性が含まれていないこと
        self.assertNotIn("token", res.json())

        # ステータスコード400が返却されていること
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # トークンを取得していない状態で認証が必要な画面にアクセスしようとしたときにエラーになること
    def test_1_12_should_get_user_profile_when_unauthorized(self):
        # ユーザー認証していない状態でprofile_urlにアクセス
        res = self.client.get(PROFILE_URL)

        # ステータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

```
