## Segmentのテスト

```python
from django.contrib.auth.models import User
from django.urls import reverse
from django.test import TestCase
from rest_framework import status
from rest_framework.test import APIClient
from .models import Segment
from .serializers import SegmentSerializer

SEGMENT_URL = "/api/segments/"


# テスト用セグメントの作成
def create_segments(segment_name):
    return Segment.objects.create(segment_name=segment_name)


# URL/idを含めたURLを取得する
def detail_url(segment_id):
    return reverse("api:segment-detail", args=[segment_id])


# セグメントのテスト（認証あり）
class AuthorizedSegmentApiTests(TestCase):
    def setUp(self):
        username = "testuser"
        password = "testuser"
        self.user = User.objects.create_user(username=username, password=password)
        self.client = APIClient()
        self.client.force_authenticate(self.user)

    # APIにGETをリクエストして取得したすべてのデータがDBに登録されたデータと一致していること
    def test_2_01_should_get_all_segment(self):
        create_segments(segment_name="SUV")
        create_segments(segment_name="Sedan")
        res = self.client.get(SEGMENT_URL)

        # Segmentのデータを全て取得し辞書型に変換
        segments = Segment.objects.all().order_by("id")
        serializer = SegmentSerializer(segments, many=True)  # 複数ある場合はmany=Trueにする

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # リクエストして取得した全てのデータがDBに登録されたデータと一致していること
        self.assertEqual(res.json(), serializer.data)

    # APIにGETをリクエストして取得した単独のデータがDBに登録されたデータと一致していること
    def test_2_02_should_get_single_segment(self):
        segment = create_segments(segment_name="SUV")
        url = detail_url(segment.pk)
        res = self.client.get(url)

        # Segmentのデータを単独で取得し辞書型に変換
        segments = Segment.objects.get(id=segment.pk)
        serializer = SegmentSerializer(segments)

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # リクエストして取得した全てのデータがDBに登録されたデータと一致していること
        self.assertEqual(res.json(), serializer.data)

    # APIにPOSTをリクエストするとセグメントが新規作成されること
    def test_2_03_should_create_new_segment_successfully(self):
        payload = {"segment_name": "K-Car"}
        res = self.client.post(SEGMENT_URL, payload)

        # APIを実行したらスタータスコード201が返却されること
        self.assertEqual(res.status_code, status.HTTP_201_CREATED)

        # 作成したセグメントがDBに存在することを確認すること
        exists = Segment.objects.filter(segment_name=payload["segment_name"]).exists()
        self.assertTrue(exists)

    # APIにセグメント名が空の状態でPOSTを実行するとセグメントが新規作成されないこと
    def test_2_04_should_not_create_new_segment_with_invalid(self):
        payload = {"segment_name": ""}
        res = self.client.post(SEGMENT_URL, payload)

        # APIを実行したらスタータスコード400が返却されること
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # APIにPATCHを実行するとセグメントが部分的に更新されること
    def test_2_05_should_partial_update_segment(self):
        segment = create_segments(segment_name="SUV")
        url = detail_url(segment.pk)

        # データを部分的に更新する
        payload = {"segment_name": "Compact SUV"}
        res = self.client.patch(url, payload)
        segment.refresh_from_db()  # DBを更新

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # 更新したセグメント名が更新内容と一致すること
        self.assertEqual(segment.segment_name, payload["segment_name"])

    # APIにPUTを実行するとセグメントが更新されること
    def test_2_06_should_update_segment(self):
        segment = create_segments(segment_name="SUV")
        url = detail_url(segment.pk)

        # データを更新する
        payload = {"segment_name": "Compact SUV"}
        res = self.client.put(url, payload)
        segment.refresh_from_db()  # DBを更新

        # APIを実行したらスタータスコード200が返却されること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # 更新したセグメント名が更新内容と一致すること
        self.assertEqual(segment.segment_name, payload["segment_name"])

    # APIにPUTを実行するとセグメントが削除されること
    def test_2_07_should_delete_segment(self):
        segment = create_segments(segment_name="SUV")

        # 作成したセグメントの数量が1であること
        self.assertEqual(1, Segment.objects.count())
        url = detail_url(segment.pk)

        # データを削除する(refreshは不要)
        res = self.client.delete(url)

        # APIを実行したらスタータスコード204が返却されること
        self.assertEqual(res.status_code, status.HTTP_204_NO_CONTENT)

        # 削除したセグメントの数量が1であること
        self.assertEqual(0, Segment.objects.count())


# セグメントのテスト（認証なし）
class UnauthorizedSegmentApiTests(TestCase):
    def setUp(self):
        self.client = APIClient()

    # 認証されていないユーザーがAPIにGETをリクエストすると401が返却されること
    def test_2_08_should_get_segment(self):
        res = self.client.get(SEGMENT_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにPOSTをリクエストすると401が返却されること
    def test_2_09_should_create_segment(self):
        res = self.client.post(SEGMENT_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにPATCHをリクエストすると401が返却されること
    def test_2_10_should_patch_segment(self):
        res = self.client.patch(SEGMENT_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにPUTをリクエストすると401が返却されること
    def test_2_11_should_put_segment(self):
        res = self.client.put(SEGMENT_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

    # 認証されていないユーザーがAPIにDELETEをリクエストすると401が返却されること
    def test_2_12_should_delete_segment(self):
        res = self.client.delete(SEGMENT_URL)

        # APIを実行したらスタータスコード401が返却されること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

```