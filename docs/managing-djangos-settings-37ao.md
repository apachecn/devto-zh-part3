# 管理 Django 的设置

> 原文：<https://dev.to/wemake-services/managing-djangos-settings-37ao>

[![django-split-settings logo](img/69a7497779014dca542bcb65037e8e6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u0ZNCO6w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ihbw852pkwmzmt2xumn4.png)

管理 Django 的设置可能有些棘手。任何 Django 开发者都会遇到几个问题。

第一个是由默认的项目结构造成的。Django 清楚地向我们提供了一个文件。乍一看，这似乎是合理的。而且刚开始用其实很好用。但是在现实世界中，这只会引起误解和沮丧。

有时，您需要在主文件中放置一些个人设置:证书路径、您的用户名或密码、数据库连接等。但是将用户特定的值放在公共设置中是一种不好的做法。其他开发人员可能会有其他设置，但这并不适用于所有人。针对这种情况最知名的黑客是`local_settings.py`。该文件放在常规设置文件附近，版本控制会忽略它。还有这几行通常放在`settings.py`结尾的某个地方:

```
try:
    from local_settings import *
except ImportError:
    # No local settings was found, skipping.
    pass 
```

看起来很简单。它有时还伴随着`local_settings.py.template`，这是版本控制，以保持您的本地设置结构是最新的。

你肯定会很快需要生产设置。你会怎么做？创建一个新文件。测试需要特殊设置吗？创建一个新文件。分期？新文件。你会有很多文件。

其次，当你有很多东西要配置的时候，你的设置文件会变得又长又重。此时，您可能会想:也许我可以将这些值分离到不同的文件中，并在不同的环境中重用它们？如果你有过这样的想法，你应该试试 django-split-settings。

## 用法

`django-split-settings`如何解决这些问题？这个助手提供了一个用户友好的界面来将你的设置保存在不同的文件中。让我们看看例子。假设您有一个现有的项目，包含`django`、`postgres`、`redis`、`rq`和电子邮件。

在我们开始之前，让我们用
安装`django-split-settings`

```
pip install django-split-settings 
```

这就是你的文件在采用`django-split-settings` :
后的样子

```
your_project/settings/
├── __init__.py
├── components
│   ├── __init__.py
│   ├── database.py
│   ├── common.py
│   ├── emails.py
│   ├── rq.py
└── environments
    ├── __init__.py
    ├── development.py
    ├── local.py.template
    ├── production.py
    └── testing.py 
```

这是基于两个因素的设置的明确分离:他们正在配置什么组件，以及我们现在正在什么环境下工作。库的灵活性允许您拥有任何想要的结构，而不仅仅是这里描述的结构。

在我们的`settings/__init__.py`中，我们可以定义任何我们想要的逻辑。基本上，我们只需定义我们希望使用哪种组件，并选择环境。这里有一个例子，我们在所有项目的生产中使用:

```
"""
This is a django-split-settings main file.
For more information read this:
https://github.com/sobolevn/django-split-settings
Default environment is `developement`.
To change settings file:
`DJANGO_ENV=production python manage.py runserver`
"""

from split_settings.tools import optional, include
from os import environ

ENV = environ.get('DJANGO_ENV') or 'development'

base_settings = [
    'components/common.py',  # standard django settings
    'components/database.py',  # postgres
    'components/rq.py',  # redis and redis-queue
    'components/emails.py',  # smtp 
    # You can even use glob:
    # 'components/*.py' 
    # Select the right env:
    'environments/{0}.py'.format(ENV),
    # Optionally override some settings:
    optional('environments/local.py'),
]

# Include settings: include(*base_settings) 
```

仅此而已。我们的应用程序将照常运行。我们用这么少的代码行实现了多个目标:

1.  我们现在已经根据他们的配置进行了单独的设置。获得可读性和可维护性
2.  我们现在已经根据环境进行了单独的设置
3.  我们现在有可选的本地设置，现在肮脏的黑客
4.  除了一些基本的重构，我们不需要做任何重构

## 现实生活中的例子

我们还创建了一个项目示例，可以作为您自己项目的模板:[https://github.com/wemake-services/wemake-django-template](https://github.com/wemake-services/wemake-django-template)

## 后记

[在 GitHub 上关注我](https://github.com/sobolevn)成为第一个知道我目前正在构建什么开源工具的人。