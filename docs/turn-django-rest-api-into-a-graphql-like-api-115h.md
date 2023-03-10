# 将 Django REST API 转换成类似 GraphQL 的 API

> 原文：<https://dev.to/robmatyszewski/turn-django-rest-api-into-a-graphql-like-api-115h>

Web APIs 是驱动当今大多数应用程序的发动机。多年来，REST 一直是 API 的主要架构师，但是在本文中，我们将研究 Django 到 GraphQL 库！

使用 REST APIs，您可以定期为每个可用的数据对象创建 URL。想想书籍的 REST API 示例——我们将拥有书籍本身、作者、奖品、人物和英雄的 URL...已经很多了！这可能会涉及很多请求。

有了 GraphQL，你就有了一个端点，问你想要什么，然后得到你想要的。根据 JS 2018 的状态，这是最受欢迎的技术，每周社区都会创建一些有趣的东西来帮助用户采用新技术。Yezyilomo 带来了 python 库的想法，允许将 Django Rest 框架转变成类似 GraphQL 的 API。让我们进一步探索。

> ![PyCoder’s Weekly profile image](img/b95ef4858574805f843c82b15ca271c3.png)PyCoder ' s Weekly@ pycoders![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)django-restql:每个 DRF 资源只动态选择一个字段子集[bit.ly/2JjYVlO](https://t.co/3JjgvRGnPm)12:12PM-2019 年 5 月 20 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1130446070260076545)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1130446070260076545)19[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1130446070260076545)47

Django-restql 是一个库，它把你用 Django REST 框架(DRF)制作的 API 转换成一个类似 GraphQL 的 API。使用 django-restql，您将能够:

*   向你的 API 发送一个查询，就能得到你所需要的，不多也不少。
*   获得预期的结果，因为您可以控制从服务器获得什么。
*   控制你得到的数据，而不是服务器。
*   节省从服务器获取未使用数据的负载。

该库通过动态选择每个 DRF 资源的字段子集来工作，如请求的`query`参数所指定的。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [叶兹伊洛莫](https://github.com/yezyilomo) / [姜戈-瑞斯特尔](https://github.com/yezyilomo/django-restql)

### 把你用 Django REST Framework(DRF)做的 API 变成 GraphQL 一样的 API。

<article class="markdown-body entry-content container-lg" itemprop="text">

# django-restql

[![Build Status](img/93795671122a735be69810bc4587990f.png)](https://api.travis-ci.com/yezyilomo/django-restql)[![Latest Version](img/e44522fabacf9aaa7bb587cfb8f12500.png)](https://pypi.org/project/django-restql/)[![Python Versions](img/a12c76a29441addf65d621d6b3cab9bd.png)](https://pypi.org/project/django-restql/)[![License](img/b12b1c5904769ffeb206191591bd4aed.png)](https://pypi.org/project/django-restql/)[![Downloads](img/89d5a3c4c6bff66bab00a06a65d4beb1.png)](https://pepy.tech/project/django-restql)[![Downloads](img/113356f3b5840f9d5fcb462af7d163aa.png)](https://pepy.tech/project/django-restql/month)[![Downloads](img/420080fb62e2f90484a29f5b9fb726d5.png)](https://pepy.tech/project/django-restql/week)

**Django RESTQL** 是一个 python 库，它允许你把用 **Django REST Framework(DRF)** 制作的 API 变成一个类似 GraphQL 的 API。使用 **Django RESTQL** ，您将能够

*   向您的 API 发送一个查询，得到您所需要的，不多也不少。

*   控制你得到的数据，而不是服务器。

*   获得可预测的结果，因为您可以控制从服务器获得什么。

*   在单个请求中获取嵌套资源。

*   避免数据的过量提取和不足提取。

*   灵活地编写(创建和更新)任何级别的嵌套数据。

是不是很酷？。

想看看这个图书馆是如何让这一切成为可能的吗？

在 https://django-restql.yezyilomo.com 查看完整文档

## 要求

*   Python >= 3.5
*   Django >= 1.11
*   Django REST 框架> = 3.5

## 安装

```
pip install django-restql
```

## 入门指南

使用 **Django RESTQL** 来查询数据非常简单，你只需要继承`DynamicFieldsMixin`类…

</article>

[View on GitHub](https://github.com/yezyilomo/django-restql)

跟随 [Yezyilomo](https://twitter.com/yezyilomo) 与他取得联系！

## 安装

```
pip install django-restql 
```

## 入门

使用 **django-restql** 非常简单，您只需在定义视图时使用 DynamicFieldsMixin。

```
from rest_framework import viewsets
from django.contrib.auth.models import User
from .serializers import UserSerializer
from django_restql import DynamicFieldsMixin

class UserViewSet(DynamicFieldsMixin, viewsets.ModelViewSet):
    queryset = User.objects.all().order_by('-date_joined')
    serializer_class = UserSerializer 
```

一个常规请求返回 DRF 序列化器上指定的所有字段，事实上 **django-restql** 根本不处理这个请求:

`GET /users`

```
 [  {  "id":  1,  "username":  "yezyilomo",  "email":  "yezileliilomo@hotmail.com",  "groups":  [1,2]  },  ...  ] 
```

**django-restql** 使用`query`参数处理所有 GET 请求，该参数用于传递响应中包含的所有字段。例如，要从`user`模型中选择`id`和`username`字段，发送一个带有`query`参数的请求，如下所示。

`GET /users/?query=[["id", "username"]]`

```
 [  {  "id":  1,  "username":  "yezyilomo"  },  ...  ] 
```

如果一个查询包含嵌套字段， **django-restql** 将返回它的 id 或 id 数组，用于嵌套的可迭代字段(one2many 或 many2many)。例如，在下面的请求中，`location`是一个平面嵌套字段(many2one)，而`groups`是一个可迭代的嵌套字段(one2many 或 many2many)。

`GET /users/?query=[["id", "username", "location", "groups"]]`

```
 [  {  "id":  1,  "username":  "yezyilomo",  "location":  6,  "groups":  [1,2]  },  ...  ] 
```

django-restql 支持查询平面和嵌套资源，因此只要您的字段在序列化器上被定义为嵌套字段，您就可以扩展或查询任何级别的嵌套字段。例如，您可以从位置查询国家和地区字段。

`GET /users/?query=[["id", "username", {"location": ["country", "region"]}]]`

```
 [  {  "id":  1,  "username":  "yezyilomo",  "location":  {  "contry":  "Tanzania",  "region":  "Dar es salaam"  }  },  ...  ] 
```

**django-restql** 也支持扩展或查询可迭代嵌套字段。例如，如果你想将`groups`字段扩展成`id`和`name`，下面是你该如何做。

`GET /users/?query=[["id", "username" {"groups": [[ "id", "name" ]]}]]`

```
 [  {  "id":  1,  "username":  "yezyilomo",  "groups":  [  {  "id":  2,  "name":  "Auth_User"  }  {  "id":  3,  "name":  "Admin_User"  }  ]  },  ...  ] 
```