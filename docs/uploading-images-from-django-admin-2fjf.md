# 已解决:从 Django-Admin 上传图片

> 原文：<https://dev.to/highcenburg/uploading-images-from-django-admin-2fjf>

当我为我的网站的一个页面选择照片时，我碰到了一个问题，我几乎完成了修复我的 Django 作品集

[![](img/e1c0ad8d373ed91d6468f17ef7bbc7d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--anmAXExF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/73l0zli66t214r6xdoox.png)

这里的问题是下拉菜单没有显示任何东西！

我的 models.py 是

```
from django.db import models

class Project(models.Model):
    title = models.CharField(max_length=100)
    description = models.TextField()
    technology = models.CharField(max_length=20)
    image = models.FilePathField(path='/img')

```

我在主项目文件夹和静态文件夹上有一个 **/img** 文件夹。

来自 StackOverflow 的人问我是否有这个文件夹的读写权限，我肯定有。

我错过什么了吗？