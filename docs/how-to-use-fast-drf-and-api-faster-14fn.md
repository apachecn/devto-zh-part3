# 如何更快地使用 fast-drf 和 API

> 原文：<https://dev.to/ashraful/how-to-use-fast-drf-and-api-faster-14fn>

### 快速 DRF(Django REST 框架)[变更日志](https://github.com/iashraful/fast-drf/blob/master/CHANGELOG.md)

> [Github 中的源码](https://github.com/iashraful/fast-drf)
> [全文档](https://ashraful.dev/docs/fast-drf/)
> Fast DRF 是一个用 Django 和 Django REST 框架让 API 开发更快的小库。
> 简单且可配置。

### 快速启动

*   使用 pip `pip install fast-drf`在您的 virtualenv 中安装库
*   将您的应用程序添加到设置上的`FAST_API_ENABLED_APPS`以获得最佳性能。喜欢，`FAST_API_ENABLED_APPS=['app_name', 'my_app']`
*   更新你的每一个模型，或者如果你使用基本抽象模型，那么它是好的，你需要更少的时间。更新如下模型:

```
from fast_drf.mixins.expose_api_model_mixin import ExposeApiModelMixin
from django.db import models

class MyModel(ExposeApiModelMixin, models.Model):
    #... All yor fields
    pass

    # The following methods are available from model mixin
    @classmethod
    def exposed_api(cls, *args, **kwargs):
        """
        This method holds a bunch of API configs and return like following...
        {
            "api_url": "",  # (REQUIRED)

            # You must use from HTTPVerbsEnum. Like HTTPVerbsEnum.GET.value, HTTPVerbsEnum.POST.value
            "allowed_methods": ['get', 'post', 'put', 'patch', 'delete'], # (NOT REQUIRED)

            # slug_field is application 'put', 'patch', 'delete' these methods
            "slug_field": "pk", # (NOT REQUIRED) DEFAULT [PK] (Must be model field, unique or primary key)

            "queryset": "",  # (NOT REQUIRED) default all
            "viewset_class": "",  # (NOT REQUIRED) BaseViewset class
            "serializer_class": "",  # (NOT REQUIRED) default BaseEntitySerializer
            "permission_classes": "",  # (NOT REQUIRED) default set from settings
        }
        :param args:
        :param kwargs:
        :return: An empty Dictionary/False OR Full config dictionary.
        """
        api_configs = {
            "api_url": 'my-model-api',
        }
        return api_configs 
```

Enter fullscreen mode Exit fullscreen mode

**就这样。**您还可以覆盖序列化程序类和视图集类