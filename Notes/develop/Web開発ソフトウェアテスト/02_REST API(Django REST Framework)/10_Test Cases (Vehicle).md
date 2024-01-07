## Vehicleのテスト

```python
from django.contrib.auth.models import User
from django.urls import reverse
from django.test import TestCase
from rest_framework import status
from rest_framework.test import APIClient
from .models import Vehicle, Brand, Segment
from .serializers import VehicleSerializer
from decimal import Decimal

SEGMENTS_URL = "/api/segments/"
BRANDS_URL = "/api/brands/"
VEHICLES_URL = "/api/vehicles/"


def create_segment(segment_name):
    return Segment.objects.create(segment_name=segment_name)


def create_brand(brand_name):
    return Segment.objects.create(brand_name=brand_name)


def create_vehicle(user, **params):
    defaults = {
        "vehicle_name": "MODEL S",
        "release_year": 2019,
        "price": 500.00,
    }
    # defaulsの上書き
    defaults.update(params)

    return Vehicle.objects.create(user=user, **defaults)


def detail_seg_url(segment_id):
    return reverse("api:segment-detail", args=[segment_id])


def detail_brand_url(brand_id):
    return reverse("api:brand-detail", args=[brand_id])


def detail_vehicle_url(vehicle_id):
    return reverse("api:vehicle-detail", args=[vehicle_id])


class AuthorizedVehicleApiTests(TestCase):
    def setUp(self):
        username = "testuser"
        password = "testuser"
        self.user = User.objects.create_user(username=username, password=password)
        self.client = APIClient()
        self.client.force_authenticate(self.user)

    # APIで取得したすべてのデータが登録されたデータと一致していること
    def test_4_01_should_get_vehicles(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")
        create_vehicle(user=self.user, segment=segment, brand=brand)
        create_vehicle(user=self.user, segment=segment, brand=brand)

        res = self.client.get(VEHICLES_URL)

        vehicles = Vehicle.objects.all().order_by("id")
        seriarizer = VehicleSerializer(vehicles, many=True)

        # ステータスコード200と一致していること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # APIで取得したすべてのデータが登録されたデータと一致していること
        self.assertEqual(res.json(), seriarizer.data)

    # APIで取得したデータが登録されたデータと一致していること
    def test_4_02_get_sinble_vechiles(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")
        vehicle = create_vehicle(user=self.user, segment=segment, brand=brand)
        url = detail_vehicle_url(vehicle.pk)
        seriarizer = VehicleSerializer(vehicle)
        res = self.client.get(url)

        # ステータスコード200と一致していること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # APIで取得したデータが登録されたデータと一致していること
        self.assertEqual(res.json(), seriarizer.data)

    # 新しく作成したデータとpayloadの内容が一致していること
    def test_4_03_create_new_vehicle_successfully(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")

        payload = {
            "vehicle_name": "MODEL S",
            "release_year": 2019,
            "price": 500.00,
            "segment": segment.pk,
            "brand": brand.pk,
        }

        res = self.client.post(VEHICLES_URL, payload)

        vehicle = Vehicle.objects.get(id=res.json()["id"])

        # ステータスコード201と一致していること
        self.assertEqual(res.status_code, status.HTTP_201_CREATED)

        # 新しく作成した内容とpayloadが一致していること
        self.assertEqual(payload["vehicle_name"], vehicle.vehicle_name)
        self.assertEqual(payload["release_year"], vehicle.release_year)
        self.assertAlmostEqual(
            Decimal(payload["price"]), vehicle.price, 2
        )  # 少数第2位まで一致しているかを評価
        self.assertEqual(payload["segment"], vehicle.segment.pk)
        self.assertEqual(payload["brand"], vehicle.brand.pk)

    # 新しく作成したデータとpayloadの内容が誤りであったとき、データが保存されていないこと
    def test_4_04_create_new_vehicle_with_invalid(self):
        payload = {
            "vehicle_name": "MODEL S",
            "release_year": 2019,
            "price": 500.00,
            "segment": "",
            "brand": "",
        }

        res = self.client.post(VEHICLES_URL, payload)

        # ステータスコード400と一致していること
        self.assertEqual(res.status_code, status.HTTP_400_BAD_REQUEST)

    # 部分的に更新した内容がpayloadと一致していること
    def test_4_05_should_partial_update_vehicle(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")
        vehicle = create_vehicle(user=self.user, segment=segment, brand=brand)
        url = detail_vehicle_url(vehicle_id=vehicle.pk)

        payload = {
            "vehicle_name": "MODEL SS",
            "release_year": 2020,
            "price": 400.11,
        }
        res = self.client.patch(url, payload)
        vehicle.refresh_from_db()  # vechileをDBの内容で再読込

        # ステータスコード200と一致していること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # 更新した内容が一致していること
        self.assertEqual(payload["vehicle_name"], vehicle.vehicle_name)
        self.assertEqual(payload["release_year"], vehicle.release_year)
        self.assertAlmostEqual(Decimal(payload["price"]), vehicle.price, 2)

    # 更新した内容がpayloadと一致していること
    def test_4_06_should_update_vehicle(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")
        vehicle = create_vehicle(user=self.user, segment=segment, brand=brand)
        url = detail_vehicle_url(vehicle_id=vehicle.pk)

        # 更新前の値がデフォルト値であること
        self.assertEqual(vehicle.vehicle_name, "MODEL S")
        self.assertEqual(vehicle.release_year, 2019)
        self.assertEqual(vehicle.price, 500.00)

        payload = {
            "vehicle_name": "MODEL SS",
            "release_year": 2020,
            "price": 400.11,
            "segment": segment.pk,
            "brand": brand.pk,
        }
        res = self.client.put(url, payload)
        vehicle.refresh_from_db()  # vechileをDBの内容で再読込

        # ステータスコード200と一致していること
        self.assertEqual(res.status_code, status.HTTP_200_OK)

        # 更新した内容が一致していること
        self.assertEqual(payload["vehicle_name"], vehicle.vehicle_name)
        self.assertEqual(payload["release_year"], vehicle.release_year)
        self.assertAlmostEqual(Decimal(payload["price"]), vehicle.price, 2)
        self.assertEqual(payload["segment"], vehicle.segment.pk)
        self.assertEqual(payload["brand"], vehicle.brand.pk)

    # データが削除されること
    def test_4_07_should_delete_vehicle(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")
        vehicle = create_vehicle(user=self.user, segment=segment, brand=brand)
        url = detail_vehicle_url(vehicle_id=vehicle.pk)

        # 削除する前はデータが存在すること
        self.assertEqual(1, Vehicle.objects.count())

        res = self.client.delete(url)

        # ステータスコード204と一致していること
        self.assertEqual(res.status_code, status.HTTP_204_NO_CONTENT)

        # 削除した後はデータが存在しないこと
        self.assertEqual(0, Vehicle.objects.count())

    # Segmentを削除したらVehicleが削除されること
    def test_4_08_should_cascade_delete_vehicle_by_segment_delete(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")
        create_vehicle(user=self.user, segment=segment, brand=brand)
        url = detail_seg_url(segment_id=segment.pk)

        # Segmentを削除する前はVehicleにデータが存在すること
        self.assertEqual(1, Vehicle.objects.count())

        self.client.delete(url)

        # Segmentを削除した後はVehicleにデータが存在しないこと
        self.assertEqual(0, Vehicle.objects.count())

    # Brandを削除したらVehicleが削除されること
    def test_4_09_should_cascade_delete_vehicle_by_brand_delete(self):
        segment = Segment.objects.create(segment_name="SUV")
        brand = Brand.objects.create(brand_name="Toyota")
        create_vehicle(user=self.user, segment=segment, brand=brand)
        url = detail_brand_url(brand_id=brand.pk)

        # Brandを削除する前はVehicleにデータが存在すること
        self.assertEqual(1, Vehicle.objects.count())

        self.client.delete(url)

        # Brandを削除した後はVehicleにデータが存在しないこと
        self.assertEqual(0, Vehicle.objects.count())


# 認証していない場合
class UnauthorizedVehicleApiTests(TestCase):
    def setUp(self):
        self.client = APIClient()

    # /api/vehiclesにアクセスできないこと
    def test_4_10_should_not_get_vehicle_when_unauhorized(self):
        res = self.client.get(VEHICLES_URL)

        # ステータスコード401と一致していること
        self.assertEqual(res.status_code, status.HTTP_401_UNAUTHORIZED)

```