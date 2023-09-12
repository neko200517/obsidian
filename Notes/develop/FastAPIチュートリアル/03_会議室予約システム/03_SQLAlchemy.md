## SQLAlchemy のインストール

```sh
pip install sqlalchemy
```

## ファイル構成

sql_app ディレクトリを作成し、以下のようなファイルを追加する。

```sh
my-app
├── sql_app
│   ├── __init__.py
│   ├── database.py
│   ├── models.py
│   ├── schemas.py
│   └── crud.py
└── app.py
```

## database.py に接続情報などを記述する

事前にDB名を sql_app としてDataBaseを作成しておく。

```python
# database.py

from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "postgresql://postgres:postgres@localhost:5432/sql_app"

engine = create_engine(SQLALCHEMY_DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
```

## models.pyを作成

- Baseを継承する
- __tablename__ = テーブル名
- 列の定義は Column を使う
- 型は Interger, String, Datetime などがある
- 主キーは primary_key = True とする
- 検索する列には index = True をつける
- ユニークな列にするには unique = True
- ForeignKey で外部キー制約を付与する
- ondelete = "SET NULL" とすることでマスタ側のデータが削除されたら自動でnullになる
- nullable = False とすることで NOT NULL と同じ制約となる

```python
# models.py 

from sqlalchemy import Column, ForeignKey, Integer, String, DateTime
from .database import Base


class User(Base):
    __tablename__ = "users"
    user_id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, index=True)


class Room(Base):
    __tablename__ = "rooms"
    room_id: Column(Integer, primary_key=True, index=True)
    room_name: Column(String, unique=True, index=True)
    capacity: Column(Integer)


class Booking(Base):
    __tablename__ = "bookings"
    booking_id: Column(Integer, primary_key=True, index=True)
    user_id: Column(
        Integer, ForeignKey("user.user_id", ondelete="SET NULL"), nullable=False
    )
    room_id: Column(
        Integer, ForeignKey("rooms.room_id", ondelete="SET NULL"), nullable=False
    )
    reserve_num: Column(Integer)
    start_datetime: Column(DateTime, nullable=False)
    end_datetime: Column(DateTime, nullable=False)
```

## schemas.py の用意

モデルの定義を schemas.py に移行する。
ORMで読み書き可能なように以下の記述を追加する。

```python
class Config:
	orm_mode = True
```

以下が完成後の schemas.py となる。

```python
# schemas.py 

import datetime
from pydantic import BaseModel, Field


class User(BaseModel):
    user_id: int
    username: str = Field(max_length=12)

    class Conifg:
        orm_mode = True


class Room(BaseModel):
    room_id: int
    room_name: str = Field(max_length=12)
    capacity: int

    class Conifg:
        orm_mode = True


class Booking(BaseModel):
    booking_id: int
    user_id: int
    room_id: int
    reserve_num: int
    start_datetime: datetime.datetime
    end_datetime: datetime.datetime

    class Conifg:
        orm_mode = True
```

## crud.py の用意 

Read する関数の定義

#### 引数

- db: 対象のセッションを指定する
- skip: 取得する行のオフセット
- limit: 取得する行数

#### 手順

1. db.query で select する
2. offset でオフセットから読み込む
3. limit で指定の数を読み込む 
4. all で複数行のレコードを取得する

```python
# crud.py 

from sqlalchemy.orm import Session
from . import models, schemas


# ユーザー一覧を取得する
def get_users(db: Session, skip: int = 0, limit: int = 0):
    return db.query(models.User).offset(skip).limit(limit).all()


# 会議室一覧を取得する
def get_rooms(db: Session, skip: int = 0, limit: int = 0):
    return db.query(models.Room).offset(skip).limit(limit).all()


# 予約一覧を取得する
def get_bookings(db: Session, skip: int = 0, limit: int = 0):
    return db.query(models.Booking).offset(skip).limit(limit).all()

```

## crud.py の用意②

Create する関数の定義

#### 引数 

- db: 対象のセッションを指定する
- shemas: FastAPIのモデルを指定する

#### 手順 

1. SqlAlchemy用のインスタンスを作成し、コンストラクタに shemas の値を与える
2. db.add で insert する
3. insert した後は commit する
4. refresh してモデルを再読み込みする

```python
# crud.py 

# 省略

# ユーザー登録
def create_user(db: Session, user: schemas.User):
    db_user = models.User(username=user.username)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user


# 会議室登録
def create_room(db: Session, room: schemas.Room):
    db_room = models.Room(room_name=room.room_name, capacity=room.capacity)
    db.add(db_room)
    db.commit()
    db.refresh(db_room)
    return db_room


# 予約登録
def create_booking(db: Session, booking: schemas.Booking):
    db_booking = models.Booking(
        user_id=booking.user_id,
        room_id=booking.room_id,
        reserve_num=booking.reserve_num,
        start_datetime=booking.start_datetime,
        end_datetime=booking.end_datetime,
    )
    db.add(db_booking)
    db.commit()
    db.refresh(db_booking)
    return db_booking
```
