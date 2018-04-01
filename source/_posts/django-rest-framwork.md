---
title: Django rest framwork
date: 2017-07-23 17:21:11
tags: django
categories:
keywords:
description:
---

RESTful API : 面向资源编程

[my-rest-framework](https://github.com/cvno/my-rest-framework)

<!-- more -->

```sh
pip3 install djangorestframework
```

# 快速使用


```python
# step 1：注册 App -> setting.py
INSTALLED_APPS = [
    ...
    'rest_framework', # <---
]
# step 2：注册路由 api--->urls.py(单独App)
from rest_framework import routers
from . import views

router = routers.DefaultRouter()
router.register('users',Views.UserInfoViewSet)

urlpatterns = [
    url(r'^'),include(router.urls)),
]

# step 3：view.py
from app01.models import *
from rest_framework.viewsets import ModelViewSet
from rest_framework.serializers import HyperlinkedModelSerializer

# Create your views here.

class UsersSerializer(HyperlinkedModelSerializer):
    ''' rest api 数据定制 指定显示的数据 '''
    class Meta:
        model = User
        fields = ('id','username')  # 字段
        # exclude = ('password',)    # 除这个字段以外
        depth = 0                   # 0 <= depth <= 10   查询外键 0 只查自己的表 1 关联的下一层也会拿到 ...

class UsersViewSet(ModelViewSet):
    queryset = User.objects.all().order_by('-id')
    serializer_class = UsersSerializer    # 验证数据库操作 相当于 Form的功能
```

# CBV

- urls.py

```python
urlpatterns = [
    url(r'user/$', views.UserView.as_view()),  # CBV
    # url(r'user/(?P<pk>[0-9]+)/$',views.UserDetail.as_view()), # CBV
    url(r'user/(\d+)/$', views.UserDetail.as_view()),  # CBV
]
```

- view.py

```python
from rest_framework.views import APIView
from app01 import serializers
from rest_framework.parsers import JSONParser   # json -> loads

class UserView(APIView):
    def get(self, request, *args, **kwargs):
        """ 获取多条数据 """
        data_list = User.objects.all()

        # 1. django 序列化
        # from django.core import serializers
        # data = serializers.serialize('json',data_list)
        # return HttpResponse(data)

        # 2. rest framework 序列化 + form 验证 + 自定义
        serializer = serializers.MySerializer(instance=data_list, many=True)  # many -> 单条 or 多条
        # return HttpResponse(json.dumps(serializer.data,ensure_ascii=False))   # 正常显示中文
        return JsonResponse(serializer.data,safe=False,json_dumps_params={"ensure_ascii":False})

    def post(self, request, *args, **kwargs):
        """ 创建数据 """
        # old
        # print(request.data)
        # Blog.objects.create(**request.data)
        # rest framework
        data = JSONParser().parse(request)
        serializer = serializers.MySerializer(data=data)
        if serializer.is_valid():
            # print(serializer.data)
            # print(serializer.errors)
            # print(serializer.validated_data)
            # 如果有 instance，则执行 update 方法；否则执行 create
            serializer.save()
        return HttpResponse('...')


class UserDetail(APIView):
    def get(self, request, nid):  # *args,**kwargs ->  pk`
        """ 获取单条数据 """
        obj = User.objects.filter(nid=nid).first()
        serializer = serializers.MySerializer(instance=obj)
        return JsonResponse(serializer.data, safe=False, json_dumps_params={"ensure_ascii": False})

    def put(self, request, nid):  # *args,**kwargs ->  pk`
        ''' 修改单条数据 '''
        obj = User.objects.filter(nid=nid).first()
        data = JSONParser().parse(request)
        serializer = serializers.MySerializer(instance=obj,data=data)
        if serializer.is_valid():
            serializer.save()
            return HttpResponse(200)
        # TODO

    def delete(self, request, nid):  # *args,**kwargs ->  pk`
        """ 删除数据 """
        obj = User.objects.filter(nid=nid).delete()
        return HttpResponse(status=204)
```

- new file serializers.py

```python
from rest_framework import serializers
from app01 import models


class MySerializer(serializers.Serializer):
    nid = serializers.IntegerField(read_only=True)
    username = serializers.CharField(max_length=64)
    password = serializers.CharField(max_length=32)
    email = serializers.CharField(max_length=32)

    # 和 Form 表单一样
    def validate_username(self, value):
        return value

    def validate_email(self, value):
        return value

    def update(self, instance, validated_data):
        instance.name = validated_data['name']
        instance.user = validated_data['user']
        instance.save()

    def create(self, validated_data):
        models.User.objects.create(**validated_data)
```


# 总结

- 2 个 url 
- 5 个 方法

*最多进行跨表查询，综合数据展示出来*

# 参考
- http://www.cnblogs.com/wupeiqi/articles/7281134.html
- http://www.cnblogs.com/OldJack/p/7294222.html

