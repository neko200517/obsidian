## api/urls.py

アプリケーション層のビューと URL を紐づける

```python
from django.urls import path
from django.conf.urls import include
from rest_framework import routers
from api.views import TaskViewSet, CreateUserView, TaskListView, TaskRetrieveView, \
    PostListView, PostRetrieveView


router = routers.DefaultRouter()
router.register('tasks', TaskViewSet, basename='tasks')

urlpatterns = [
  path('list-post/', PostListView.as_view(), name="list-post"),
  path('detail-post/<str:pk>/', PostRetrieveView.as_view(), name="detail-post"),
  path('list-task/', TaskListView.as_view(), name="list-task"),
  path('detail-task/<str:pk>/', TaskRetrieveView.as_view(), name="detail-task"),
  path('register/', CreateUserView.as_view(), name='register'),
  path('auth/', include('djoser.urls.jwt')),
  path('', include(router.urls)),
]
```

## rest_api/urls.py 

プロジェクト層の URL に アプリケーション層の URL を追加して参照できるようにする

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
]
```