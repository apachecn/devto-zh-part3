# 用 Django 发送电子邮件的最简单方法(使用 AWS 的 SES)

> 原文：<https://dev.to/hmlon/the-easiest-way-to-send-emails-with-django-using-ses-from-aws-3gl8>

你想在你的申请中发送电子邮件吗？通知、时事通讯、促销，发送电子邮件的理由太多了。

读完这篇文章后，你就可以用你的 Django 应用程序发送任何种类的邮件了。

使用不同的电子邮件发送服务，您可以通过多种方式发送电子邮件。但在本指南中，我将介绍最流行的服务之一，SES，或简单的电子邮件服务。它是由亚马逊开发的，可以免费用于每月多达 62000 封电子邮件。

为了使用 SES，我们将使用 [`django-ses`](https://github.com/django-ses/django-ses) 库。它非常容易设置和使用。

但是在我们开始之前，我们需要...

# 报名上 AWS

如果您还没有 AWS 帐户，您需要获得一个。没有它，你根本无法使用 SES。

要注册，您需要前往[AWS 注册页面](https://portal.aws.amazon.com/billing/signup#/start)。

# 设置

我想你已经有一个 Django 项目了。要安装`django-ses`库，您需要运行以下命令

```
pip install django-ses 
```

一旦安装了这个库，你需要通过在你的`settings.py`
中添加下面一行来告诉 Django 使用这个库作为电子邮件后端

```
EMAIL_BACKEND = 'django_ses.SESBackend' 
```

然后，您需要获得 se 的凭据

1.  转到 AWS
2.  选择 IAM 服务
3.  添加用户
4.  选择用户名和“编程访问”类型(获取访问密钥 ID 和秘密访问密钥)
5.  附加`AmazonSESFullAccess`权限
6.  创造用户

之后，您应该会看到“访问密钥 ID”和“秘密访问密钥”。像这样把它们加到`settings.py`上

```
AWS_ACCESS_KEY_ID = 'YOUR-ACCESS-KEY-ID'
AWS_SECRET_ACCESS_KEY = 'YOUR-SECRET-ACCESS-KEY' 
```

# 发送邮件

[![](img/a673fe1d09b437ff61cb5058e1a934a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xxpOnJWK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1479920252409-6e3d8e8d4866%3Fixlib%3Drb-1.2.1%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

现在我们已经准备好了所有的凭证，我们可以发送一封电子邮件了。我们可以使用 Django 的核心功能来做到这一点( [`django.core.mail.send_mail`](https://docs.djangoproject.com/en/2.1/topics/email/) 更具体一点)。

```
from django.core.mail import send_mail
send_mail(
    'Subject here',
    'Here is the message.',
    'from@example.com',
    ['to@example.com']
) 
```

此电子邮件应使用 SES 发送。

所以让我们跳进 Django 的壳里去试试吧。

```
python manage.py shell
>>> from django.core.mail import send_mail
>>> send_mail(
...    'Subject here',
...    'Here is the message.',
...    'from@example.com',
...    ['to@example.com']
...)
boto.ses.exceptions.SESAddressNotVerifiedError: SESAddressNotVerifiedError: 400 Email address is not verified.
<ErrorResponse xmlns="http://ses.amazonaws.com/doc/2010-12-01/">
  <Error>
    <Type>Sender</Type>
    <Code>MessageRejected</Code>
    <Message>Email address is not verified. The following identities failed the check in region US-EAST-1: from@example.com</Message>
  </Error>
  <RequestId>3966220a-3c6c-11e9-909d-889357e40867</RequestId>
</ErrorResponse> 
```

哦不！我们出错了…谁会想到我们不能从`from@example.com`发送电子邮件。让我们解决这个问题。

# 从您的个人邮箱发送邮件

[![](img/d1c5e751e25bd349401743f8064f570c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5TscrBrz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1529454488831-3c13b1640074%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

首先，我们可以从您的个人电子邮件地址发送电子邮件。如果你正在构建一个 MVP，这应该是可以接受的。

如果你不想让每个人都看到你的个人邮件，你可以像`your.application@gmail.com`一样为你的应用程序创建一个单独的邮件地址。如果人们出于某种原因开始阻止你的电子邮件，这不会影响你的个人电子邮件健康。

要从您的电子邮件地址发送电子邮件，您需要

1.  转到 AWS
2.  选择 SES 服务
3.  转到“电子邮件地址”选项卡
4.  单击“验证新的电子邮件地址”
5.  输入您选择的电子邮件地址
6.  转到您指定的电子邮件的收件箱
7.  确认您想要在 SES 中使用您的电子邮件

一旦完成，您应该能够从 Django shell 发送电子邮件

```
>>>send_mail(
...    'Subject here',
...    'Here is the message.',
...    'your.application@gmail.com',
...    ['your.email.address+to.test@example.com']
...)
1 
```

之后，您应该会收到一封电子邮件

[![img](img/f04a13c386bab3fc16d11071af15a35a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A5vACC-b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2019-03-04-the-easiest-way-to-send-emails-with-django/email_from_your_personal_email_address.png)

您可能已经注意到了一个由`send_mail`方法返回的数字。这是成功发送的电子邮件的数量，在我们的例子中是 1。

如果我们试图向某个地址发送电子邮件，但失败了，那么失败的电子邮件将不会被计算在内

```
>>> send_mail(
...     'Subject here',
...     'Here is the message.',
...     'your.application@gmail.com',
...     ['your.email.address+to.test@example.com', 'not.existing.email.for.real.asdvsrg@gmail.com']
... )
1 
```

这个`send_mail`调用返回 1，因为它无法向第二个地址(`not.existing.email.for.real.asdvsrg@gmail.com`)发送电子邮件，因为它不存在。我刚编的。

# 从自定义域发送电子邮件

[![](img/e9622bd30c0530be82e89a12eb1796bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hsAdr49S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1543448360-6b80281205f2%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

一旦你的 MVP 开始工作，你的应用程序有了一个域名，就可以从这个域名发送电子邮件了。为此，您需要将该域添加到 SES

1.  转到 AWS
2.  选择 SES 服务
3.  转到“域”选项卡
4.  单击“验证新域”

如果你已经在 AWS Route53 上有了你的域名，那么你只需点击进入，你的域名就准备好了。

如果您在其他地方设置了域名，您需要遵循指定的说明。

一旦您正确地按照说明操作，您应该会看到绿色的已验证状态，并且该域应该准备好通过 SES 发送电子邮件。我们可以回到 Django shell，测试从新的闪亮域名发送电子邮件

```
>>>send_mail(
...    'Subject here',
...    'Here is the message.',
...    'hello@yourdomain.com',
...    ['your.email.address+to.test@example.com']
...)
1 
```

这是你的电子邮件

[![img](img/50237e7eb6456e884a5c109bfb85d2c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2xgMsX1l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2019-03-04-the-easiest-way-to-send-emails-with-django/email_from_your_domain.png)

很漂亮，对吧？但它可能会更漂亮…

# 自定义邮件中的名称

你有没有注意到一个漂亮的电子邮件名称？类似这样的东西

[![img](img/f17fb272a0c2ed0148bbf79d5b608599.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMNEu7eB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2019-03-04-the-easiest-way-to-send-emails-with-django/email_with_custom_name_example.png)

它甚至包括一个取消订阅的链接。你也可以很酷。这很容易。你只需要修改发件人姓名。在开头添加一个用双引号括起来的名字，并在最后的`<>`中添加一个电子邮件地址，就像这样`”Desired name” <email@example.com>`。

```
>>>send_mail(
...    'Subject here',
...    'Here is the message.',
...    '"Hello, you" <hello@yourdomain.com>',
...    ['your.email.address+to.test@example.com']
...)
1 
```

现在你也有个很酷的名字了

[![img](img/445d186d25895575d4428c38d18d563e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fD1d7A8E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2019-03-04-the-easiest-way-to-send-emails-with-django/email_with_custom_name.png)

它可以是你想要的任何东西。

# 结论

现在你应该知道如何

*   建立`django-ses`库
*   用它来发送电子邮件
*   在 SES 中设置电子邮件
*   在 SES 中设置域
*   使电子邮件发件人信息更漂亮

这是关于[门](http://musicnotifier.com/)系列文章的第六部分。请继续关注第七部分。你可以在我的 [GitHub 页面](https://github.com/hmlON)找到[这个项目](https://github.com/hmlON/mun)的代码，还有我的其他项目。如果你喜欢这篇文章，请在下面留下你的评论并关注我。