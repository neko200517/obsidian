## api/models.py

Post, Task テーブルの定義

```python
from django.db import models

class Post(models.Model):
  title = models.CharField(max_length=50)
  content = models.CharField(max_length=500)
  created_at = models.DateTimeField(auto_now_add=True)

  def __str__(self):
    return str(self.id) + ' - ' + self.title


class Task(models.Model):
  title = models.CharField(max_length=50)
  created_at = models.DateTimeField(auto_now_add=True)

  def __str__(self):
    return str(self.id) + ' - ' + self.title
```

## マイグレーション

db.sqlite3 に以下のテーブルが作成される

```bash
python manage.py makemigrations
python manage.py migrate
```