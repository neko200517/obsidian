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

Model を dict 型に変換するシリアライザーを作成

## シリアライザーの作成

```python
# api/serialiser.py

from rest_framework import serializers
from .models import Segment, Brand, Vehicle
from django.contrib.auth.models import User


class UserSerializer(serializers.ModelSerializer):
    # Metaクラスを定義
    class Meta:
        # model: モデルを指定
        # fields: シリアライズ機能でmodelの値をdict型に変換
        # extra_kwargs: バリデーション
        model = User
        fields = ["id", "username", "password"]
        extra_kwargs = {
            "password": {"write_only": True, "required": True, "min_length": 5}
        }

    # ユーザーを作成する（パスワードを暗号化する）
    def create(self, validated_data):
        # **を付与することでdict型のvalidated_dateを展開して引数として渡す
        user = User.objects.create_user(**validated_data)
        return user


class SegmentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Segment
        fields = ["id", "segment_name"]


class BrandSerializer(serializers.ModelSerializer):
    class Meta:
        model = Brand
        fields = ["id", "brand_name"]


class VehicleSerializer(serializers.ModelSerializer):
    # serializers.ReadOnlyFieldで直接取得
    segment_name = serializers.ReadOnlyField(
        source="segment.segment_name", read_only=True
    )
    brand_name = serializers.ReadOnlyField(source="segment.brand_name", read_only=True)

    class Meta:
        model = Vehicle
        fields = [
            "id",
            "vehicle_name",
            "release_year",
            "price",
            "segment",
            "brand",
            "segment_name",
            "brand_name",
        ]
        # userを関連付ける
        extra_kwargs = {"user": {"read_only": True}}

```
