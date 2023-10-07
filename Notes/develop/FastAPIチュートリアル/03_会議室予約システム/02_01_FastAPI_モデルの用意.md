## ファイル構成

例として、以下のようなファイル構成にする。

```sh
my-app
├── models
│   ├── __init__.py
│   ├── bookings.py
│   ├── rooms.py
│   └── users.py
└── app.py
```

## Users

```python
from pydantic import BaseModel, Field
import datetime 

class User:
	user_id: int
	user_name: str = Field(max_length=12)
```

## Room

```python
from pydantic import BaseModel 
import datetime 

class Room:
	room_id: int 
	room_name: str 
	capacity: int
```

## Booking

```python
from pydantic import BaseModel 
import datetime

class Booking(BaseModel):
	booking_id: int 
	user_id: int 
	room_id: int 
	reserved_numj: int
	start_date_time: datetime.datetime
	end_date_time: datetime.datetime
```

## インポート用に__init__.pyを作成する

__init__.pyを作成し、各ファイルを読み込む

```python
# __init__.py

from .users import User
from .rooms import Room
from .bookings import Booking
```

## app.py から定義を呼び出す

```python
# app.py

from models import User, Room, Booking
# 省略
```
