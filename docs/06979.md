# Django 环境变量

> 原文：<https://dev.to/serhatteker/django-environment-variables-2hjp>

如果你看看现代代码部署实践，比如在[12 因素
应用](https://12factor.net/)中，环境变量对于保密信息或者为服务器设置的特定信息非常重要。同时在设置文件(文件或每台服务器和主机)中没有长设置。它有助于使您的代码保持简单和整洁，以下内容引自 12factorapp 网站:

> *   Handle resources to databases, Memcached and other background services.
> *   Vouchers to external services such as Amazon S3 or Twitter
> *   Each deployment value is the canonical hostname of the deployment.

## 什么是环境变量？

环境变量是可以影响程序运行方式的键:值对。它们需要在流程运行前的某个时间点进行设置，以便流程可以读取它们并采取相应的行动。在生产环境中，您的数据库名称和密码经常被设置为环境变量，这样信息就不会出现在某个代码存储库中。不幸的是，有时甚至是公开的。

通过依赖一个环境变量，你的代码并不关心你的设置是什么，因为你可以在其他地方设置它们。实际上，使用环境变量非常简单。你只需要开始导入原生 os 模块:
`import os`。

```
import os

name = os.environ.get('USER') 
```

Enter fullscreen mode Exit fullscreen mode

好的，但是在你的操作系统中，在哪里放置和找到这些变量呢？有很多方法，但我认为以下两种是最好的实现方式:

### 1 -虚拟环境

最好的方法之一是从虚拟环境中获取它们。当您激活虚拟环境`source .venv/bin/activate`时，“激活”脚本运行。您可以将以下脚本添加到该激活文件的末尾:

```
export $(grep -v '^#' ./.env | xargs) 
```

Enter fullscreen mode Exit fullscreen mode

其中`./`是你的项目“根目录”。因此，如果你有不止一个`env`文件，你也可以添加如下:

```
export $(grep -v '^#' ./.env | xargs)
export $(grep -v '^#' ./.envs/.env.dev | xargs) 
```

Enter fullscreen mode Exit fullscreen mode

我通常使用这种方法，但这需要一个`Unix-like OS` (MacOS & Linux 等)。

### 2 - Django-Environ 模块库

[django-environ 模块](https://github.com/joke2k/django-environ)允许您使用[十二因素
方法](https://12factor.net/)来配置带有
环境变量的 django 应用程序。

在您的项目`settings.py`中

```
import environ

env = environ.Env()

# reading .env file env.read_env(env.str('./', '.env'))

# https://docs.djangoproject.com/en/dev/ref/settings/#secret-key SECRET_KEY = env('DJANGO_SECRET_KEY') 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，你还可以通过`.bashrc`或者`.bash_profile`甚至`.profile`来导出你的`env vars`，但是这是一个不好的做法:在系统范围内使用项目范围的限制。