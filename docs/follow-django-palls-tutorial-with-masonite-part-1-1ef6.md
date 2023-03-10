# 跟随 Django 投票教程与 Masonite 第 1 部分

> 原文：<https://dev.to/kentaro0919/follow-django-palls-tutorial-with-masonite-part-1-1ef6>

# 编写您的第一个 ~~Django~~ Masonite 应用程序，第 1 部分

跟随[教程 01](https://docs.djangoproject.com/en/2.1/intro/tutorial01/#writing-your-first-django-app-part-1)

[Github](https://github.com/kentaro0919/masonite_polls)

## 检查您的安装。

在 Django 中，您运行以下命令来检查您的 Django 版本。

```
$ python -m django --version 
```

在 Masonite 中，您运行

```
$ craft -v 
```

## 创建项目

在姜戈。

```
$ django-admin startproject mysite 
```

在 Masonite

```
$ craft new mysite 
```

## 开发服务器

在姜戈。

```
$ python manage.py runserver 
```

在 Masonite 中，我们需要在运行服务器之前安装。
[运行 WSGI 服务器](https://docs.masoniteproject.com/the-craft-command/introduction#running-the-wsgi-server)
[主机和端口](https://docs.masoniteproject.com/the-craft-command/introduction#host-and-port)

```
$ craft install
# then now we can run the server.
$ craft serve 
```

## 创建投票应用程序

到目前为止，Django 和 Masonite 并没有太大的不同，只是针对您的代码运行一些命令。从这一点来看，区别就很明显了。
跑步

在姜戈。

```
$ python manage.py startapp polls 
```

在 Masonite，我们不需要这个。

```
$ 
```

## 写出你的第一个观点

在姜戈。

```
# polls/views.py from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.") 
```

在梅索尼特。[控制器](https://docs.masoniteproject.com/the-basics/controllers)像 Django 中的“视图”一样工作。
首先我们必须添加新的控制器。

```
$ craft controller Polls 
```

该命令生成“app/http/controllers/polls controller . py”

```
""" A PollsController Module """

class PollsController:
    """PollsController
    """

    def show(self):
        pass 
```

让我们编辑如下

```
""" A PollsController Module """

class PollsController:
    """PollsController
    """

    def index(self):
        return "Hello, world. You're at the polls index." 
```

在 Django 中，urls.py 处理来自用户的请求。

```
# mysite/urls.py from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
] 
```

```
#polls/urls.py from django.urls import path

from  . import views

urlpatterns = [
    path('', views.index, name='index'),
] 
```

在 Masonite 中，路由处理请求。默认情况下，您的项目中有 routes
/web.py。
[路由](https://docs.masoniteproject.com/the-basics/routing)

```
"""Web Routes."""

from masonite.routes import Get, Post

ROUTES = [
    Get().route('/', 'WelcomeController@show').name('welcome'),
] 
```

请求是“[http://localhost:8000/polls/](http://localhost:8000/polls/)”，所以我们必须在我们的路由中处理/polls/
。Masonite 处理 HTTP 动词并与控制器匹配。

```
"""Web Routes."""

from masonite.routes import Get, Post

ROUTES = [
    Get().route('/', 'WelcomeController@show').name('welcome'),
    Get().route('/polls/', 'PollsController@index'),
] 
```

确保您正在运行服务器

```
$ craft serve -r 
```

然后，您可以访问您的页面。

[![In your browser](img/8849d6e52c0296c16217af8b95c85e88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oy44mnqI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iy2rs78idoaa79m4lswl.png)

我们和 Masonite 一起成功地完成了 Django 教程的 tutorial01。