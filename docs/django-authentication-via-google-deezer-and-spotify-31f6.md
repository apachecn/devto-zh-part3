# 通过 Google、Deezer 和 Spotify 进行 Django 认证

> 原文：<https://dev.to/hmlon/django-authentication-via-google-deezer-and-spotify-31f6>

在本教程中，我们将添加注册功能，通过 Google、Deezer 和 Spotify 登录，查看成功屏幕，然后退出。我们的项目将是关于音乐发布通知。

在之前的教程中，我已经创建了一个带有登陆页面的 Django 项目。

## 我们要怎么做呢？

我们将使用一个[社交应用 django 库](https://github.com/python-social-auth/social-app-django)。它支持[大量的授权提供商](https://python-social-auth.readthedocs.io/en/latest/intro.html#auth-providers)。在图书馆中，它们也被称为“后端”。正如你所猜测的，我们将需要谷歌、Deezer 和 Spotify。

要安装该库，请遵循官方安装指南。

## 将 social-app-django 添加到 django 项目中

首先，你需要在你的项目的`settings.py`
中添加库到我们的`INSTALLED_APPS`

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # ...
    'social_django',
    # ...
    'pages',
] 
```

之后，通过运行
迁移您的数据库

```
python3 manage.py migrate 
```

然后将`social_django.urls`添加到您项目的`urls.py`

```
urlpatterns = [
    # ...
    path('social/', include('social_django.urls')),
    # ... ] 
```

还有`SOCIAL_AUTH_URL_NAMESPACE`到你项目的`settings.py`再到

```
SOCIAL_AUTH_URL_NAMESPACE = 'social' 
```

现在您应该什么都没有了，但是我们已经准备好向项目添加一个 auth provider。要了解更多信息，你可以阅读 Django 的官方 python-social-auth 安装指南。

## 通过 Google(+)认证

[![](img/1880b388f64bb951edf8a4293c57555e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hZnRtoY_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1529612700005-e35377bf1415%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D45b2666d6486f1e881e2fe341b0728e8%26auto%3Dformat%26fit%3Dcrop%26w%3D1950%26q%3D80)

首先，您需要获得应用程序的凭证

1.  访问[谷歌 API 控制台](https://console.developers.google.com/)
2.  转到凭据
3.  创建凭据
4.  将`http://127.0.0.1:8000/social/complete/google-plus/`添加到**授权重定向 URIs**
5.  一旦有了生产重定向 URL，您必须在这里添加它

现在你应该在你的应用程序中看到**客户端 ID** 和**客户端秘密**。将它们添加到项目的`settings.py`

```
SOCIAL_AUTH_GOOGLE_PLUS_KEY = 'Your Client ID'
SOCIAL_AUTH_GOOGLE_PLUS_SECRET = 'Your Client secret' 
```

将 Google 添加到项目的`settings.py`
中的`AUTHENTICATION_BACKENDS`

```
AUTHENTICATION_BACKENDS = (
    'social_core.backends.google.GooglePlusAuth',
) 
```

最后，在你的菜单、标题或任何你希望用户点击登录的地方添加一个通过谷歌登录的链接

```
<a href="{% url "social:begin" "google-plus" %}">Google</a> 
```

更多关于谷歌认证如何工作的阅读，这里是官方指南。

## 通过 Deezer 认证

[![](img/4201ef87b97ec92eb06ab9abb1b2a983.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EOnMmhrg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1527150122806-f682d2fd8b09%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3Dada3761ade3c12bdfc3af876f4e28667%26auto%3Dformat%26fit%3Dcrop%26w%3D2089%26q%3D80)

Deezer 认证的过程与谷歌的非常相似。首先，获取凭证

1.  访问[面向开发者的 Deezer](https://developers.deezer.com/myapps)
2.  创建应用程序
3.  将`http://127.0.0.1:8000/social/complete/deezer/`设置为**认证后重定向 URL**
4.  同样，一旦有了生产重定向 URL，您必须在这里添加它

现在你应该在你的应用中看到**应用 id** 和**密钥**。将它们添加到项目的`settings.py`

```
SOCIAL_AUTH_DEEZER_KEY = 'Your Application id'
SOCIAL_AUTH_DEEZER_SECRET = 'Your Secret Key' 
```

再次，添加 Deezer 到项目的`settings.py`
中的`AUTHENTICATION_BACKENDS`

```
AUTHENTICATION_BACKENDS = (
    'social_core.backends.google.GooglePlusAuth',
    'social_core.backends.deezer.DeezerOAuth2',
) 
```

此外，还有一个通过 Deezer
登录的链接

```
<a href="{% url "social:begin" "deezer" %}">Deezer</a> 
```

#### 还有一件事

你可能需要一些权限。例如，Deezer 默认不提供用户的电子邮件。您需要为`email`请求许可。你可以在[官方权限文档](https://developers.deezer.com/api/permissions)中查看可用权限的完整列表。

使用`social-app-django`库添加所需的权限非常简单。你只需要在项目的`settings.py`中给一个`scope`常量添加你想要的权限。在 Deezer 情况下，常数必须称为`SOCIAL_AUTH_DEEZER_SCOPE`

```
SOCIAL_AUTH_DEEZER_SCOPE = ['basic_access', 'email'] 
```

## 通过 Spotify 认证

[![](img/1073941d8dcc86912da54fde313e4682.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MuZX5-lb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1532354058425-ba7ccc7e4a24%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D117db81ba4f77871de8d2320d6661f9e%26auto%3Dformat%26fit%3Dcrop%26w%3D1650%26q%3D80)

Spotify 认证就像 Deezer 一样类似于 Google。

1.  访问[面向开发者的 Spotify】](https://developer.spotify.com/dashboard/applications)
2.  创建客户端 ID
3.  将`http://127.0.0.1:8000/social/complete/spotify/`设置为**重定向 URIs**
4.  同样，一旦有了生产重定向 URL，您必须在这里添加它

现在你应该在你的应用中看到**客户端 ID** 和**客户端秘密**。将它们添加到项目的`settings.py`

```
SOCIAL_AUTH_SPOTIFY_KEY = 'Your Client ID'
SOCIAL_AUTH_SPOTIFY_SECRET = 'Your Client Secret' 
```

再次，将 Spotify 添加到项目的`settings.py`
中的`AUTHENTICATION_BACKENDS`

```
AUTHENTICATION_BACKENDS = (
    'social_core.backends.google.GooglePlusAuth',
    'social_core.backends.deezer.DeezerOAuth2',
    'social_core.backends.spotify.SpotifyOAuth2',
) 
```

同样，还有一个通过 Spotify 登录的链接

```
<a href="{% url "social:begin" "spotify" %}">Spotify</a> 
```

关于权限，我需要`user-read-email`和`user-library-read`。你可以在[官方权限文档](https://developer.spotify.com/documentation/general/guides/scopes/)中查看可用权限的完整列表。

```
SOCIAL_AUTH_SPOTIFY_SCOPE = ['user-read-email', 'user-library-read'] 
```

## 签到后的生活

通常，在登录之后，显示某种仪表板或提要或任何你的应用程序的主要目的是一个好主意。我将展示所有最新音乐发行的屏幕。

首先，让我们创建一个`releases`应用程序

```
python3 manage.py startapp releases 
```

将应用程序添加到项目的`settings.py`
中已安装的应用程序

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'social_django',
    'pages',
    'releases',
] 
```

在项目的`urls.py`
中添加项目的 URL

```
urlpatterns = [
    # ...
    path('', include('releases.urls')),
    # ... ] 
```

在`releases/urls.py`
中定义一个`/releases` URL

```
from django.urls import path
from . import views

urlpatterns = [
    path('releases', views.index, name='index'),
] 
```

在`releases/views.py`中创建一个视图

```
def index(request):
    return render(request, 'releases/index.html') 
```

创建一个简单的模板来表明我们关心在`releases/templates/releases/index.html`(我讨厌不得不写两次`releases`)
的用户

```
<h1>Latest Releases</h1>
<p>We care about you, but we don't have anything done yet.</p> 
```

现在我们有了在登录后重定向用户的地方，剩下唯一要做的事情——在项目的`settings.py`
中设置一个`LOGIN_REDIRECT_URL`

```
LOGIN_REDIRECT_URL = '/releases' 
```

添加此行后，所有用户登录后应直接转到`/releases` URL。

## 修复重复用户

[![](img/d246ffd86c7b30b4a7cb0bdf8dc6f5eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s759w_h4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1512746755088-5cf7d5565d9e%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D70f0d929ebd7ff35392a2166d97425f4%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

现在我们可以用几个不同的帐户登录了，但是有一个问题，我们不能对重复的用户，也就是有相同电子邮件的用户做任何事情。我们可以通过两种不同的服务注册两次(比如 Google 和 Spotify)来快速检查。

然后我们可以通过运行 Django shell
来检查是否有 2 个用户

```
python3 manage.py shell 
```

并检查那里的当前用户数量

```
from django.contrib.auth.models import User
User.objects.count() 
```

我们还可以调查它们之间有什么不同

```
vars(User.objects.all()) 
```

要解决这个问题，我们必须多了解一点`social-app-django`是如何工作的。

当用户登录时，他们会通过一个所谓的管道。下面是默认管道的样子

```
(
    # Get the information we can about the user and return it in a simple
    # format to create the user instance later. On some cases the details are
    # already part of the auth response from the provider, but sometimes this
    # could hit a provider API.
    'social_core.pipeline.social_auth.social_details',

    # Get the social uid from whichever service we're authing thru. The uid is
    # the unique identifier of the given user in the provider.
    'social_core.pipeline.social_auth.social_uid',

    # Verifies that the current auth process is valid within the current
    # project, this is where emails and domains whitelists are applied (if
    # defined).
    'social_core.pipeline.social_auth.auth_allowed',

    # Checks if the current social-account is already associated in the site.
    'social_core.pipeline.social_auth.social_user',

    # Make up a username for this person, appends a random string at the end if
    # there's any collision.
    'social_core.pipeline.user.get_username',

    # Send a validation email to the user to verify its email address.
    # Disabled by default.
    # 'social_core.pipeline.mail.mail_validation', 
    # Associates the current social details with another user account with
    # a similar email address. Disabled by default.
    # 'social_core.pipeline.social_auth.associate_by_email', 
    # Create a user account if we haven't found one yet.
    'social_core.pipeline.user.create_user',

    # Create the record that associates the social account with the user.
    'social_core.pipeline.social_auth.associate_user',

    # Populate the extra_data field in the social record with the values
    # specified by settings (and the default ones like access_token, etc).
    'social_core.pipeline.social_auth.load_extra_data',

    # Update the user record with any changed info from the auth service.
    'social_core.pipeline.user.user_details',
) 
```

如果您愿意，您可以阅读管道每个阶段的描述。但目前让我们感兴趣的是第 7 步，`social_core.pipeline.social_auth.associate_by_email`，默认是禁用的。它所做的就是

> 将当前社交详细信息与另一个具有相似电子邮件地址的用户帐户相关联

听起来正是我们需要的。只需取消对该行的注释，并将管道复制到项目的`settings.py`

```
SOCIAL_AUTH_PIPELINE = (
    'social_core.pipeline.social_auth.social_details',
    'social_core.pipeline.social_auth.social_uid',
    'social_core.pipeline.social_auth.auth_allowed',
    'social_core.pipeline.social_auth.social_user',
    'social_core.pipeline.social_auth.associate_by_email',
    'social_core.pipeline.user.create_user',
    'social_core.pipeline.social_auth.associate_user',
    'social_core.pipeline.social_auth.load_extra_data',
    'social_core.pipeline.user.user_details',
) 
```

你可以在[阅读更多关于管道](https://python-social-auth.readthedocs.io/en/latest/pipeline.html)的官方文件。我们将在本系列的下一部分构建一个定制管道。

## 签退

[![](img/b9245c29491e0d9575ef4babeaea30d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IHmShX8I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1520033906782-1684d0e7498e%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D93f0c662e61a6cb38c09aa0d7696d12c%26auto%3Dformat%26fit%3Dcrop%26w%3D1934%26q%3D80)

您可能希望为您的用户添加注销功能。我不建议这样做，因为这只是用户离开你的网站的另一个机会，但如果你仍然想添加它，这很容易。

Django 已经内置了一个名为`django.contrib.auth`的认证支持。现在，我们只需要它的注销部分。

首先，将`django.contrib.auth.urls`添加到您项目的`urls.py`

```
urlpatterns = [
    # ...
    path('', include('django.contrib.auth.urls')),
    # ... ] 
```

然后，在你的项目的`settings.py`中设置`LOGOUT_REDIRECT_URL`到`'/'`，以便用户在退出
后被重定向到主页

```
LOGOUT_REDIRECT_URL = '/' 
```

现在，剩下的就是在你想要的地方添加一个退出链接

```
<a href="{% url 'logout' %}" class="header-link">Log out</a> 
```

## 只显示相关链接

但是现在你的用户可以看到所有的登录链接和注销链接。你可能不希望一个用户登录或退出两次，这意味着什么？所以我们只展示相关链接。

Django 的用户模型有一个我们可以使用的特殊属性，叫做 [`is_authenticated`](https://docs.djangoproject.com/en/2.1/ref/contrib/auth/#django.contrib.auth.models.User.is_authenticated) 。我们可以在模板中使用一个简单的`if`，并检查是否有一个`user`，一个已经在模板中可用的变量`is_authenticated`。

```
{% if user.is_authenticated %}
    <a href="/logout">Log out</a>
{% else %}
    <a href="{% url "social:begin" "google-plus" %}">Google</a>
    <a href="{% url "social:begin" "deezer" %}">Deezer</a>
    <a href="{% url "social:begin" "spotify" %}">Spotify</a>
{% endif %} 
```

## 结论

在我报道的这个故事中:

*   如何使用`social-app-django`库
*   如何创建应用程序，如何在 Google、Deezer 和 Spotify 中设置自定义范围，以及如何将这些服务集成到您的应用程序中
*   如何创建一个简单的成功页面在登录后显示
*   什么是`social-app-django`库中的管道
*   以及如何让用户注销

这是关于模拟世界系列文章的第二部分。请继续关注第三部分。你可以在我的 [GitHub 页面](https://github.com/hmlON)找到[这个项目](https://github.com/hmlON/mun)的代码，以及我的其他项目。如果你喜欢这篇文章，请在下面留下你的评论并关注我。