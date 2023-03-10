# django:django-allauth 的基本授权

> 原文：<https://dev.to/hmartinezdev/django-basic-auth-with-django-allauth-3p7b>

联合内容！用 django-allauth 检查原始的 [Django: basic auth。](https://hectormartinez.dev/posts/django-getting-started/)

Django 是一个 web 框架，它附带了很多为您构建的东西，因此您可以专注于构建您的应用程序，而不是 web 框架。Django 还有一个庞大的应用生态系统，你可以在你的 Django 项目中使用它！这里您将使用`django-allauth`包，它将为您处理认证过程。你将很快拥有一个运行中的 Django 认证网站！

## 准备环境:virtualenv 和 Django

开发 Python 的最佳实践之一是使用虚拟环境。通过使用它们，您已经隔离了 Python 解释器和它们各自的包。这允许你有两个应用程序在不同的版本上使用同一个包，没有任何问题，每个应用程序都有自己的虚拟环境。

从 Python 3.3 开始，标准库中有一个名为`venv`的模块可以创建虚拟环境。还有一个叫`virtualenv`的包也有同样的功能。创建一个文件夹，试着在里面创建一个虚拟环境:

```
$ mkdir django-authweb
$ cd django-authweb
$ python3 -m venv venv 
```

如果最后一个命令像这样失败:

```
The virtual environment was not created successfully because ensurepip is not
available. On Debian/Ubuntu systems, you need to install the python3-venv
package using the following command.

    apt-get install python3-venv

You may need to use sudo with that command. After installing the python3-venv
package, recreate your virtual environment.

Failing command: ['/venv/bin/python3', '-Im', 'ensurepip', '--upgrade', '--default-pip'] 
```

您有两个选择:按照这些说明运行`apt-get install python3-venv`或者用`python3 -m pip install virtualenv`安装 virtualenv。第二种情况创造虚拟环境:

```
$ virtualenv -p python3 venv 
```

你现在应该有一个工作的虚拟环境。激活它。一个视觉标记应该出现在你的提示中，表明你在一个虚拟环境中:

```
$ source venv/bin/activate
(venv) $ # This is the visual clue
(venv) $ pip install django 
```

## Django app 骨架

Django 是来加速你的开发的，Django 本身承担了很多事情，所以它附带了一些工具来帮助你为你的项目创建一个功能结构。您将使用`django-admin`来创建您的应用程序的框架:

```
(venv) $ django-admin startproject authweb .
(venv) $ tree
.
├── venv/ # The virtual environment files
├── authweb
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py 
```

现在，您可以使用`manage.py`脚本:
启动开发服务器

```
(venv) $ python manage.py runserver 
```

打开 [http://localhost:8000/](http://localhost:8000/) ，您应该会看到这个页面:

[![](img/2b896a8fffe3c5572cd7657d520fda0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D2VsECo7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/django-getting-started/landing.png)

## 安装登录验证

虽然从满足感和获得知识的角度来看，为一个问题实现你的定制解决方案是有益的，但有时使用现有的解决方案更好。`django-allauth`附带模板、电子邮件验证、密码重置和更多功能，可帮助您减少实施这些功能所花费的时间。在这里，您将使用它来处理用户的认证。使用 pip:
安装

```
(venv) $ pip install django-allauth 
```

现在您应该修改`authweb/settings.py`文件，以便启用它并使它工作(`documentation <https://django-allauth.readthedocs.io/en/latest/installation.html>` _)。

```
[...] # Default content of settings.py

AUTHENTICATION_BACKENDS = [
    "django.contrib.auth.backends.ModelBackend",
    "allauth.account.auth_backends.AuthenticationBackend",
]

INSTALLED_APPS += [
    "django.contrib.sites",
    "allauth",
    "allauth.account",
    "allauth.socialaccount",
    "authweb",
]

LOGIN_REDIRECT_URL = "home"
SITE_ID = 1
AUTH_USER_MODEL = "authweb.User"
ACCOUNT_EMAIL_VERIFICATION = 'mandatory'
ACCOUNT_EMAIL_REQUIRED = True
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
TEMPLATES[0]["DIRS"] += [os.path.join(BASE_DIR, "templates")] 
```

在这里，您要做几件事情:将 allauth 添加到您的应用程序中，告诉 Django 您将使用定制的用户模型，强制进行电子邮件验证并打印发送到控制台的电子邮件，告诉模板引擎在项目根目录下名为“templates”的文件夹中进行搜索，等等。所有这些将使您的应用程序暂时工作。

## 定制用户模型

现在您将创建新的文件，这些文件将从 Django 给出的缺省值创建一个新的用户模型。您还将在管理后端注册新用户。这是[在开始新项目](https://docs.djangoproject.com/es/2.1/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project)时推荐的，也许你永远不需要改变用户，但这样做你会掩护你的背部。

```
(venv) $ touch authweb/admin.py authweb/models.py
(venv) $ tree
.
├── venv/
├── authweb
│   ├── __init__.py
│   ├── admin.py # New
│   ├── models.py # New
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py 
```

从`models.py`开始:

```
# authweb/models.py
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    pass 
```

这里，您扩展了默认的用户实现，但没有添加任何东西。如果需要的话，你可以在这里为你的用户编写新的属性。现在的`admin.py` :

```
# authweb/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin

from .models import User

admin.site.register(User, UserAdmin) 
```

随着自定义用户的创建，这里它被添加到 Django 的管理部分。

## 用于测试的基本视图

现在您已经添加了新的用户模型，并在 admin 部分注册了它，您可以开始创建一个登录页面了。在这里你可以找到一个测试的基本模板。Allauth 附带了大部分模板，但是登录页面应该由用户提供。创建一个`authweb/views.py`文件:

```
# authweb/views.py
from django.views.generic import TemplateView

class Home(TemplateView):
    template_name = "home.html" 
```

现在在根文件夹中创建一个名为`templates`的目录，里面有一个`home.html`文件:

```
(venv) $ tree
.
├── venv/
├── authweb/
├── templates/ # This
│   └── home.html # This
└── manage.py 
```

并将它粘贴到 HTML 文件:

```
{% raw -%}
{% load account socialaccount %}
<body>
    <h1>Django Allauth Tutorial</h1>
    {% if user.is_authenticated %}
    <p>Welcome {{ user.username }} !!!</p>
        {% if user.has_usable_password %}
        <p><a href="{% url 'account_change_password' %}">Change password</a></p>
        {% else %}
        <p><a href="{% url 'account_set_password' %}">Set password</a></p>
        {% endif %}
    <a href="{% url 'account_logout' %}">Logout</a>
    {% else %}
    <a href="{% url 'account_login' %}">Sign In</a> or <a href="{% url 'account_signup' %}">Sign up</a>
    {% endif %}
</body>{% endraw %} 
```

然后在`urls.py`文件中使用这个视图，在这里我们也添加了 Django AllauthURLs(默认情况下是 admin URL):

```
# authweb/urls.py
from django.contrib import admin
from django.urls import include
from django.urls import path

from .views import Home

urlpatterns = [
    path("admin/", admin.site.urls),
    path("accounts/", include("allauth.urls")),
    path("", Home.as_view(), name="home"),
] 
```

## 迁徙

创建迁移(数据库中的更改)，针对数据库运行它们。这将创建相关的表，以便您的应用程序存储信息。

```
(venv) $ python manage.py makemigrations authweb
Migrations for 'authweb':
    authweb/migrations/0001_initial.py
        - Create model User

(venv) $ python manage.py migrate
Operations to perform:
    Apply all migrations: account, admin, auth, authweb, contenttypes, sessions, sites, socialaccount
Running migrations:
    Applying contenttypes.0001_initial... OK
    [...]
    Applying socialaccount.0003_extra_data_default_dict... OK 
```

## 测试！

用`python manage.py runserver`启动开发服务器并注册一个新用户。单击注册，填写字段并注册。

<figure>[![Sign up your new account.](img/0266d23e70f8f5b88c0b770148f7484e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--db_VvKSP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/django-getting-started/signup.png) 

<figcaption>

注册您的新账户。

</figcaption>

</figure>

<figure>[![Verify the email usign the terminal.](img/2023b4bee1dfe1c0c9faa83c38cc5628.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z367XIZe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/django-getting-started/verify.png) 

<figcaption>

使用终端验证邮件。

</figcaption>

</figure>

然后，您应该会在终端中看到一封类似这样的电子邮件:

```
Subject: [example.com] Please Confirm Your E-mail Address
From: webmaster@localhost
To: username@example.com

Hello from example.com!

You're receiving this e-mail because user username has given yours as an e-mail address to connect their account.

To confirm this is correct, go to http://127.0.0.1:8001/accounts/confirm-email/MQ:1gsnl3:iiag-ubYZzp08arceovMGYvxbQA/

Thank you from example.com!
example.com
------------------------------------------------------------------------------- 
```

转到邮件中的 URL，您的电子邮件就成功通过了验证。

<figure>[![Confirm your email.](img/5e65aed569ae8ec0e889581f09720c33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V-_x1eox--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/django-getting-started/confirm.png) 

<figcaption>

确认你的邮件。

</figcaption>

</figure>

<figure>[![Log in with your account.](img/1f70c199b752738b3b22ea3ee4fc50dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YhGTaCRK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/django-getting-started/login.png) 

<figcaption>

用你的账号登录。

</figcaption>

</figure>

<figure>[![Great! You are in!](img/2dd1d1d636c023c29f3f10f2217a1dcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WD0_u0q---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dimg/django-getting-started/logged.png) 

<figcaption>

好棒！你被录取了！

</figcaption>

</figure>

使用 allauth，您可以限制某些操作来验证电子邮件或使电子邮件验证具有随机性。查看 Allauth 文档以了解更多信息。

本帖到此结束！现在，您可以定制 allauth 模板、注册过程等。我希望这是有用的，让我在下面的评论中知道！