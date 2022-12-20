# faastRuby 的新 Hello World——Ruby & Crystal 的无服务器平台——第 1 部分。

> 原文：<https://dev.to/sergiosouzalima/the-new-hello-world-for-faastruby-the-serverless-platform-for-ruby-crystal-part-1-2o95>

[![alt text](img/af1fd29649cee0c1ad3d50cc17c574ee.png "faastRuby logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--2IyWzhJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://faastruby.io/wp-content/uploads/2019/03/logo-negative-1.png)

嗨伙计们！我想和你们分享云计算最新兴的技术之一。我说的是“功能即服务”或 FaaS。在这篇文章中，我想让你和我一起探索一个介绍性的解决方案。我们将使用 [faastRuby](https://www.faastruby.io) 平台，一步一步地构建一个无服务器应用程序，并且我们将使该解决方案在云中可用。

如果您不熟悉这些术语，请不要担心。在这篇文章中，我用[葡萄牙语](https://onebitcode.com/faastruby-serverless-ruby/)和[英语](https://faastruby.io/blog/building-a-simple-landing-page-with-faastruby-functions/)描述了更多关于 [faastRuby](https://www.faastruby.io) 、FaaS 和无服务器的内容。

faastRuby 是一个令人难以置信的平台，让我们可以轻松地使用无服务器功能。该平台非常直观，与 Heroku 非常相似，Heroku 的服务器基础设施是完全抽象的。

点击此处 [https://www.faastruby.io](https://www.faastruby.io) 了解更多 faastruby。

## 简介

faastRuby 平台更新了，0.5 版本后有改进，所以我决定重写 Hello World 教程。

### 了解平台

faastRuby 是一个面向 Ruby 和 Crystal 的无服务器软件开发平台，由两个主要组件组成。

#### 主要成分

1.  **faastRuby Cloud** -在这里您可以部署您的应用程序和功能。
2.  faastRuby Local(-一个 SDK，它允许您在您的机器上开发和测试丰富的 web 应用程序和 API，并将您的应用程序作为一组分布式功能部署到平台上。基本上你要做的就是写代码。不需要操作。

#### 基本概念:**账户、项目、功能&工作区**

1.  **账户**——要开始使用 faastRuby 云平台，你必须创建一个账户，你将使用这个账户登录平台。faastRuby 帐户属于个人或公司。
2.  **函数** - faastRuby 应用程序是用纯红宝石或水晶函数构建的。功能是 faastRuby 应用程序的部署单元。每个功能都是独立的——它们相互独立。
3.  **项目** -项目是你的应用程序所在的地方。您的项目将有一个主文件夹、子文件夹、静态文件和函数。
4.  **工作区** -工作区是一个隔离的环境，您可以在其中部署您的功能。它们由类似的 URL 表示。

#### 分解一个工作区 URL

例。

*   **项目名称**:你的 faastRuby 项目的名称。
*   **环境** : Stage 为默认环境。你可以选择另一个像 prod，dev 等。
*   这是项目标识符，用来确保你的工作区有唯一的名字。不应该改变。

## 我们要创造什么？

使用 faastRuby 资源，我们将在本地创建两个 Ruby 函数，测试它们，并将它们部署到云中。

## 工具

*   **ruby 2.6.0**
*   **宝石 faastruby**

## 逐步创建功能

### 安装

为 faastRuby 项目创建您自己的文件夹，并进入其中。这不是强制性的。我更喜欢使用这种文件夹组织，这样，所有在 faastRuby 做的项目都留在这个文件夹里。

```
$ mkdir faastruby
$ cd faastruby 
```

Enter fullscreen mode Exit fullscreen mode

仅当您使用 Ruby 版本管理器时才执行此步骤， [RVM](https://rvm.io) :

```
$ faastruby/> rvm use ruby-2.6.0@faastruby --ruby-version --create 
```

Enter fullscreen mode Exit fullscreen mode

#### 安装宝石

如果您没有安装 gem，请执行:

```
$ faastruby/> gem install faastruby 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经安装了 faastruby gem，只需升级它:

```
$ faastruby/> faastruby update 
```

Enter fullscreen mode Exit fullscreen mode

要知道是否有效，让我们看看宝石版。对我来说这是当前版本:0.5.23。请注意，您的版本可能会更新，因为平台会不断更新。

```
$ faastruby/> faastruby -v
0.5.23 
```

Enter fullscreen mode Exit fullscreen mode

### 在 faastRuby 平台报名

```
$ faastruby/> faastruby signup

Welcome to FaaStRuby! Please enter your email address:
Email: <<< you@your-email.com >>> 
```

Enter fullscreen mode Exit fullscreen mode

输入邮箱后，就可以用密码进入了。

```
Now type in a password. 
It must contain 8 to 50 characters and have at least 
one uppercase letter, one lowercase letter, 
one number and one special character @ $ ! % * ? &
************
✔ Creating your account... 
```

Enter fullscreen mode Exit fullscreen mode

然后，您将收到一封带有验证令牌的电子邮件。将令牌复制并粘贴到下面所示的字段中。

```
You should have received an email with a confirmation token.
If you didn't receive an email:
- Make sure you sign up with the correct email address
- The system won't send an email if you account status is already 'confirmed'
Confirmation Token: <<< your token here >>> 
```

Enter fullscreen mode Exit fullscreen mode

最后，您将注册并登录平台。

```
✔ Confirming your account...
Login successful! 
```

Enter fullscreen mode Exit fullscreen mode

当你需要登录时，使用下面的命令:

```
$ faastruby/> faastruby login       
Email: you@your-email.com
Password: ***********
Login successful. 
```

Enter fullscreen mode Exit fullscreen mode

而当你需要注销的时候，可以使用:

```
$ faastruby/> faastruby logout ## use --all to logout from all machines
Logout successful. 
```

Enter fullscreen mode Exit fullscreen mode

#### 本文继续[第 2 部分](https://dev.to/sergiosouzalima/the-new-hello-world-for-faastruby-the-serverless-platform-for-ruby-crystal-part-2-29pk)，我们将构建一个 faastRuby 项目并将其部署到云中。