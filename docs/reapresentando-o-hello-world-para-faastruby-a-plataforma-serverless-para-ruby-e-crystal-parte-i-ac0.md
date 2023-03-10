# 一个无服务器的红宝石水晶平台。

> 原文：<https://dev.to/sergiosouzalima/reapresentando-o-hello-world-para-faastruby-a-plataforma-serverless-para-ruby-e-crystal-parte-i-ac0>

[![alt text](img/af1fd29649cee0c1ad3d50cc17c574ee.png "faastRuby logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--2IyWzhJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://faastruby.io/wp-content/uploads/2019/03/logo-negative-1.png)

你好，开发商朋友们！我想和大家分享云计算的一项新兴技术。我说的是"服务功能"或" FaaS "。在这篇文章中，我想让你们和我一起探索一个介绍性的解决方案。我们将使用平台 [faastRuby](https://www.faastruby.io) 逐步构建无服务器功能，并在云中提供解决方案。

如果你不熟悉这些术语，不用担心，我在这篇文章中更多地描述了 [faastRuby](https://www.faastruby.io) ，FaaS 和 Serverless 分别在[葡萄牙语](https://onebitcode.com/faastruby-serverless-ruby/)和[英语](https://faastruby.io/blog/building-a-simple-landing-page-with-faastruby-functions/)中。

[faastRuby](https://www.faastruby.io) 平台令人惊叹，使我们能够轻松地使用无服务器功能。该平台非常直观，与 Heroku 非常相似，在 heroku 中，所有服务器基础架构都是抽象的。

有关此平台的更多信息，请访问 faastRuby 创作公司网站: [https://www.faastruby.io](https://www.faastruby.io)

我们走！

## 导言

faastRuby 平台在 0.5 版之后进行了更新和改进，因此我需要重写 Hello World。本文是对前一篇文章的更新。它是一个使用新功能构建基本设计的教程。

### 了解平台

**faastRuby** 是 Ruby 和 Crystal 的无服务器软件开发平台，由两个主要组件组成。

#### 主要成分

1.  **【faastruby cloud】**—是部署它们的平台，其应用程序和功能所在的位置。
2.  **本地 faastruby**—一个软件开发工具包(SDK)，允许在本地计算机上开发和测试 web 应用程序和 API，并将应用程序作为一组分布式功能部署到平台上。不需要部署或基础架构操作。

#### 概念 básicos: **账户、项目、功能 e 工作区**

1.  **账户** -使用 faastRuby 平台需要有账户。每个帐户都属于一个人或公司，该人或公司必须通过其电子邮件、密码和将生成的令牌来标识自己。
2.  **功能**—是源代码形式的解决方案所在。功能相互独立。对于 Ruby 和 Crystal，我们使用方法作为函数。
3.  **项目**项目是应用所在的地方。项目将具有父文件夹、子文件夹、静态文件和角色。
4.  **工作区**—是云中的孤立空间或环境。部署的目标是 Workspaces，而这是函数所在的位置。Workspaces 由如下 URL 表示:。

#### 详细介绍工作区 URL

example。

*   **专案名称**:专案的名称。
*   **环境** : Stage 是默认环境。您可以选择其他选项，例如 prod、dev 等。
*   **abs123** :为每个项目自动生成的唯一标识符。不应该改变。

## 我们要创造什么？

使用 faastRuby 平台，我们将在 Ruby 中创建两个角色，并在 internet 上快速提供它们。

## 我们需要的工具

*   **ruby 2.6.0**
*   **宝石 faastruby**

## 项目创建的逐步

### 设施

为 faastRuby 项目创建自己的文件夹，然后进入该文件夹。这不是必需的。我更喜欢使用这种文件夹组织方式，所以在 faastRuby 所做的所有项目都保存在此文件夹中。

```
$ mkdir faastruby
$ cd faastruby 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 Ruby 版本管理器， [RVM](https://rvm.io) :

```
$ faastruby/> rvm use ruby-2.6.0@faastruby --ruby-version --create 
```

Enter fullscreen mode Exit fullscreen mode

#### 镶嵌宝石

如果尚未安装 gem，请执行以下操作:

```
$ faastruby/> gem install faastruby 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经安装了 gem faastruby，则只需更新:

```
$ faastruby/> faastruby update 
```

Enter fullscreen mode Exit fullscreen mode

要确定安装是否成功，请参阅 gem faastruby 版本。您的版本可能较新，因为 gem 经常更新。

```
$ faastruby/> faastruby -v
0.5.21 
```

Enter fullscreen mode Exit fullscreen mode

### 注册域名

```
$ faastruby/> faastruby signup

Welcome to FaaStRuby! Please enter your email address:
Email: you@your-email.com 
```

Enter fullscreen mode Exit fullscreen mode

输入电子邮件后，系统会提示您输入密码。

```
Now type in a password. 
It must contain 8 to 50 characters and have at least 
one uppercase letter, one lowercase letter, 
one number and one special character @ $ ! % * ? &
************
✔ Creating your account... 
```

Enter fullscreen mode Exit fullscreen mode

然后，您将收到一封带有令牌的电子邮件进行确认。将令牌复制并粘贴到下面的字段中。

```
You should have received an email with a confirmation token.
If you didn't receive an email:
- Make sure you sign up with the correct email address
- The system won't send an email if you account status is already 'confirmed'
Confirmation Token: <<< seu token aqui >>> 
```

Enter fullscreen mode Exit fullscreen mode

然后您将在平台上注册和登录。

```
✔ Confirming your account...
Login successful! 
```

Enter fullscreen mode Exit fullscreen mode

下次需要登录时，请使用命令:

```
$ faastruby/> faastruby login       
Email: you@your-email.com
Password: ***********
Login successful. 
```

Enter fullscreen mode Exit fullscreen mode

并且当你需要注销时可以使用:

```
$ faastruby/> faastruby logout ## use --all para deslogar de todas máquinas
Logout successful. 
```

Enter fullscreen mode Exit fullscreen mode

#### 本文继续在[第二部分](https://dev.to/sergiosouzalima/reapresentando-o-hello-world-para-faastruby-a-plataforma-serverless-para-ruby-e-crystal-parte-ii-31c5)中进行，我们将从这里开始创建项目。