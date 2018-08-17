首先安装 djangorest_framework以及django-filter,

在setting里面添加app, rest_framework,

定义url

```python
from django.conf.urls import url
from rest_framework.routers import SimpleRouter
# 最后导入自己写的包或者库
from app import views

router = SimpleRouter()
router.register(r'^student', views.StudentSource)

urlpatterns = [
    url(r'^hello/$', views.hello, name='hello'),
    url(r'^students/$', views.students, name='students'),
]
urlpatterns += router.urls
```

在views里面写简单的CRUD

```
from rest_framework import mixins, viewsets
...
...
class StudentSource(mixins.CreateModelMixin,  # 增 post
                    mixins.DestroyModelMixin,  # 删 delete
                    mixins.UpdateModelMixin,  # 改 update/put
                    mixins.ListModelMixin,  # 查 get
                    mixins.RetrieveModelMixin,
                    viewsets.GenericViewSet,):
    # 查询资源的所有数据
    queryset = Student.objects.all().filter(is_delete=0).order_by('id')
    # 序列化
    serializer_class = StudentSerializer # 自定义继承类
    # 过滤
    filter_class = StudentFilter # 自定义继承类

    # 重构删除函数 实现软删除
    def perform_destroy(self, instance):
        instance.is_delete = 1
        instance.save()

    # 重构更新函数  自动添加修改时间
    def update(self, request, *args, **kwargs):
        partial = kwargs.pop('partial', False)
        instance = self.get_object()
        instance.operator_time = timezone.localtime(timezone.now())
        serializer = self.get_serializer(instance, data=request.data, partial=partial)
        serializer.is_valid(raise_exception=True)
        self.perform_update(serializer)
        return Response(serializer.data)

    # 重构
    def retrieve(self, request, *args, **kwargs):
        # 第二次改造
        try:
            instance = self.get_object()
            serializer = self.get_serializer(instance)
            return Response(serializer.data)
        except:
            data = {
                'code': 203,
                'msg': '学生不存在'
            }
        return Response(data)
```

在setting里面配置rest_framework的配置

```
REST_FRAMEWORK = {
    # 重构返回数据结构
    'DEFAULT_RENDERER_CLASSES': (
        'utils.returnRenderer.MyJsonRenderer',
    ),
    # 定义分页
    # 'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    # 'PAGE_SIZE': 2,
    # 配置过滤
    'DEFAULT_FILTER_BACKENDS': ('rest_framework.filters.DjangoFilterBackend',
                                'rest_framework.filters.SearchFilter'),
}
```

自定义继承函数,在app里面创建app_serializer.py文件

```
from rest_framework import serializers

from app.models import Student


# 做返回的数据结构处理
class StudentSerializer(serializers.ModelSerializer):
    # 自定义错误提示
    s_name = serializers.CharField(error_messages={'blank': '姓名不能为空'})

    class Meta:
        model = Student
        fields = ['id', 's_name', 's_sex', 's_age', 'operator_time']

    # 将返回的数据进行处理
    def to_representation(self, instance):
        # 调用父类进行序列化
        data = super().to_representation(instance)
        data['s_name'] = 'Python1804:' + data['s_name']
        return data
```

自定义继承函数,在app里面创建app_filter.py文件

```
import django_filters
from rest_framework import filters

from app.models import Student

# 做返回的数据内容处理
class StudentFilter(filters.FilterSet):
    # 过滤s_name参数,精确过滤 lookup_expr属性值就是我们要使用的django的orm的方法
    s_name = django_filters.CharFilter('s_name', lookup_expr='icontains')  # 模糊查询 加i,不区分大小写
    # 获取s_age大于等于s_age_min小于等于s_age_max
    s_age_min = django_filters.NumberFilter('s_age', lookup_expr='gte')  # 大于
    s_age_max = django_filters.NumberFilter('s_age', lookup_expr='lte')  # 小于

    class Meta:
        model = Student
        fields = ['s_name', 's_age']
```

