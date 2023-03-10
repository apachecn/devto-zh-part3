# 使用 django-allauth 让 Google 登录任何 django 应用程序

> 原文：<https://dev.to/tomwerneruk/using-django-allauth-for-google-login-to-any-django-app-2b2l>

如果你正在为你的新项目使用 django-cookiecutter (如果你不是，你应该这样做),你可能知道它提供了开箱即用的用户登录管理，由 django-allauth 支持。这个包为您提供了构建自己的用户管理体验的框架。

我最近一直在开发的一个应用程序，我希望允许通过谷歌登录，但希望确保只有预先批准的用户才能使用社交登录登录——该应用程序是为封闭的用户组设计的，而不是为随机公众设计的。本质上只有通过邀请才能访问。我尝试了 django-invitations，但是感觉它更适合非社交登录(抱歉伙计们，如果我错卖了一个看起来很棒的库！).因此，回到了定制 django-allauth。

本教程并不打算介绍如何让 djang-allauth 从头开始工作——要么看一眼 django-cookiecutter 源代码，要么按照文档中的说明进行安装。

## 改变行为

django-allauth 允许通过使用适配器覆盖默认行为来构建定制功能。使用 cookiecutter？已经在`<project name>/users/adapters.py`下为您创建了一个自定义适配器存根。如果没有，继续在你的项目中的一个 Django 应用程序下创建一个 adapters.py 文件——如果你有一个“核心”或“设置”应用程序，这可能是它的最佳位置。位置并不重要，因为您必须在 Django 设置中指定位置。

```
...
ACCOUNT_ADAPTER = 'myapp.users.adapters.AccountAdapter'
SOCIALACCOUNT_ADAPTER = 'myapp.users.adapters.SocialAccountAdapter'
... 
```

_ **settings.py** _

```
from allauth.account.adapter import DefaultAccountAdapter
from allauth.socialaccount.adapter import DefaultSocialAccountAdapter
from django.conf import settings
from django.http import HttpRequest

class AccountAdapter(DefaultAccountAdapter):

    def is_open_for_signup(self, request: HttpRequest):
        return getattr(settings, "ACCOUNT_ALLOW_REGISTRATION", True)

class SocialAccountAdapter(DefaultSocialAccountAdapter):

    def is_open_for_signup(self, request: HttpRequest, sociallogin: Any):
        return getattr(settings, "ACCOUNT_ALLOW_REGISTRATION", True) 
```

_ **adapters.py** _

这段代码片段的所有功劳都归于 django-cookiecutter 开发人员，它很简单，也很容易理解。目前，基于帐户和社交的登录都是开放的，这个适配器目前对操作没有净影响。

我的要求是只允许社交登录和注册，所以首先，让我们禁用基于帐户的注册。

```
...
class AccountAdapter(DefaultAccountAdapter):

    def is_open_for_signup(self, request: HttpRequest):
        return getattr(settings, "ACCOUNT_ALLOW_REGISTRATION", False)
... 
```

_ **adapters.py** _

如果你去`http://app/accounts/signup/`，你会被告知注册已经结束。太好了。下一步是让社交登录发挥作用。以谷歌为例，你需要做到以下几点；

*   确保您已经运行了`./manage.py migrate`以确保所需的社交登录表已经创建，
*   确保您已将`'allauth.socialaccount.providers.google',`添加到 INSTALLED_APPS 中
*   前往并创建您的 [Google OAuth 凭证](https://console.developers.google.com/apis/credentials)，
*   登录你的 Django 管理员，创建一个新的“Google”类型的社交应用对象。

如果你前往`http://app/accounts/login/`，谷歌应该被列为登录提供商。测试您的谷歌登录，它应该允许您登录您的谷歌帐户没有任何问题。将在用户和社交帐户下创建一个新用户。

我们就快成功了，最后一步是只限制“预先批准”的用户。继续操作，通过 Django Admin 删除刚刚创建的社交帐户对象(将用户留在原处)。我们将限制预批准用户的方式是改变 SocialAccountAdapter 行为。我们只想从一个已经创建了有效用户对象的电子邮件地址进行社交登录(通过 Django Admin 或另一个管理屏幕手动创建)。

```
class SocialAccountAdapter(DefaultSocialAccountAdapter):

    def pre_social_login(self, request, sociallogin):
        try:
            get_user_model().objects.get(email=sociallogin.user.email)
        except get_user_model().DoesNotExist:
            from django.contrib import messages
            messages.add_message(request, messages.ERROR, 'Social logon from this account not allowed.') 
            raise ImmediateHttpResponse(HttpResponse(status=500))
        else:
            user = get_user_model().objects.get(email=sociallogin.user.email)
            if not sociallogin.is_existing:
                sociallogin.connect(request, user) 

    def is_open_for_signup(self, request, sociallogin):        
        return True 
```

这是我们更新的社交适配器。`is_open_for_signup`仍然返回 true，因为即使是预授权用户也仍然会在第一次登录时点击注册代码路径。当社交登录完成时，但在转换到有效的 Django 会话之前，调用`pre_social_login`。因此，这是检查预授权的要点。

try 块根据社交登录请求提供的电子邮件查找现有用户。如果用户存在，链接社交登录并继续，如果不存在，中止尝试并在“消息”队列中留下更新。

就是这样，这应该给预批准的社交登录一个依据。一些改进，使这一生产准备就绪；

*   确保您强制进行电子邮件验证，因为一些社交网络(如脸书)已被标记为具有可疑的电子邮件地址验证程序。通过强制电子邮件验证，这是一个额外的保护步骤，
*   开始覆盖默认模板，为您决定使用的每个提供商提供登录按钮/徽标，
*   通过取消对用户名的需求，使用户体验更加流畅。

帮助您入门的建议配置；

```
ACCOUNT_AUTHENTICATION_METHOD = 'email'
ACCOUNT_EMAIL_REQUIRED = True
ACCOUNT_EMAIL_VERIFICATION = 'mandatory'
ACCOUNT_ADAPTER = 'myapp.users.adapters.AccountAdapter'
SOCIALACCOUNT_ADAPTER = 'myapp.users.adapters.SocialAccountAdapter'
SOCIALACCOUNT_QUERY_EMAIL = True
SOCIALACCOUNT_AUTO_SIGNUP = True
ACCOUNT_USERNAME_REQUIRED = False 
```

目前就这些。下面一如既往的评论和提问！