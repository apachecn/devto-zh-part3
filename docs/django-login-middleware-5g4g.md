# Django 登录中间件

> 原文：<https://dev.to/serhatteker/django-login-middleware-5g4g>

Django 提供了一个名为`login_required`的装饰器，它获取您的一个视图，并阻止用户查看它，除非他们已经过身份验证。每次我们需要在视图上方添加这个装饰器时。通常在复杂站点的视图中使用`login_required`装饰器是一件非常痛苦的事情。如果您忘记将它添加到包含敏感信息的视图中，该怎么办？

Django 允许您编写访问每个请求的定制中间件，这样您就可以添加适用于整个站点的功能。我的中间件只是拦截每个请求，并将用户重定向到站点登录页面，如果他们还没有登录的话。它还允许您给出例外情况，即无需认证即可查看的页面。

首先创建`LoginRequiredMiddleware.py`文件:

```
import re

from django.conf import settings
from django.http import HttpResponseRedirect
from django.utils.deprecation import MiddlewareMixin
from django.utils.http import is_safe_url

EXEMPT_URLS = [re.compile(settings.LOGIN_URL.lstrip('/'))]
if hasattr(settings, 'LOGIN_EXEMPT_URLS'):
    EXEMPT_URLS += [re.compile(url) for url in settings.LOGIN_EXEMPT_URLS]

class LoginRequiredMiddleware(MiddlewareMixin):
    def process_request(self, request):
        assert hasattr(request, 'user'), "The Login Required Middleware"
        if not request.user.is_authenticated:
            path = request.path_info.lstrip('/')
            if not any(m.match(path) for m in EXEMPT_URLS):
                redirect_to = settings.LOGIN_URL
                # 'next' variable to support redirection to attempted page after login
                if len(path) > 0 and is_safe_url(
                    url=request.path_info, allowed_hosts=request.get_host()):
                    redirect_to = f"{settings.LOGIN_URL}?next={request.path_info}"

                return HttpResponseRedirect(redirect_to) 
```

Enter fullscreen mode Exit fullscreen mode

将这个中间件添加到项目的核心目录中。如:

```
└── project_root
    ├── manage.py
    ├── src
    │   ├── api
    │   ├── app
    │   └── core
    │   │   └── LoginRequiredMiddlware.py
    │   ├── static
    │   ├── templates
    │   └── users
    └── tests
        └── test_users.py 
```

Enter fullscreen mode Exit fullscreen mode

然后把这个中间件添加到你的设置文件中，`settings.py`。

```
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    # login middleware
    'src.core.middleware.LoginRequiredMiddleware',
] 
```

Enter fullscreen mode Exit fullscreen mode

如果您想添加其他例外页面，即无需认证即可查看的页面，您可以将这些 URL 添加到您设置中的`LOGIN_EXEMPT_URLS`:

```
LOGIN_EXEMPT_URLS = (
    r'^home/$',
    r'^register/$',
    r'^pricing/$',
) 
```

Enter fullscreen mode Exit fullscreen mode

页（page 的缩写）s:`LoginRequiredMiddleware`需要安装认证中间件。编辑您的`MIDDLEWARE`设置以插入`'django.contrib.auth.middleware.AuthenticationMiddleware'`。如果这不起作用，确保你的`TEMPLATE_CONTEXT_PROCESSORS`设置包括`'django.core.context_processors.auth'`

更多详情:
[中间件](https://docs.djangoproject.com/en/dev/ref/settings/#middleware)和[模板 _ 上下文 _ 处理器](https://docs.djangoproject.com/en/dev/ref/settings/#template-context-processors)