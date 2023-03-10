# 轻松升级到 PHP 7.3

> 原文：<https://dev.to/wilburpowery/easily-upgrade-to-php-73-bdo>

[![Thumbnail](img/a785d61386669c0b62610bddb19b20fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k62YeU0m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/wilburpowery/image/upload/v1545503015/easily-upgrade-to-php-73.png)

首发于我的[网站](https://wilburpowery.co/posts/easily-upgrade-to-php-73/)。

PHP 7.3 已经发布了几个星期，如果你有兴趣升级，这个过程非常简单。

> **注意:**这个小指南是为 Mac OS 用户准备的。如果你是 Windows 用户，快速的谷歌搜索应该能帮到你！

## 让我们更新一切

为了拉安装或更新任何应用程序或服务，我使用一个名为 [Homebrew](https://brew.sh/) 的 Mac OS 软件包管理器。在我们开始升级 PHP 安装之前，让我们快速运行一个自制程序命令来更新我们的公式和自制程序本身。

```
$ brew update 
```

### 升级 PHP

现在我们的公式已经更新了，我们可以继续用家酿上可用的最新版本升级我们的 PHP 安装。

```
$ brew upgrade php 
```

升级完成后，您可以在您的终端中打开一个新标签，并使用`php -v`检查您的 PHP 版本。如果一切顺利，您应该会看到类似这样的内容:

```
PHP 7.3.0 (cli) (built: Dec  7 2018 11:00:11) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.0-dev, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.0, Copyright (c) 1999-2018, by Zend Technologies 
```

只需几分钟，您就已经升级到了 PHP 的最新版本。请记住，您在系统或应用程序中使用的一些包或服务可能还没有更新到 PHP 7.3。

## Laravel 用户奖励

如果你是 Laravel 的用户，你很可能使用 Laravel 代客服务。为了让 Valet 在 PHP 7.3 上工作，你只需要把它升级到最新发布的版本。只需做一个`composer global update`来更新所有全球安装的 Composer 包，然后只需做一个`valet install`。

再次运行`valet install`是必要的，以配置安装来与您的新 PHP 版本一起工作。

> **警告:**在 Laravel 框架中排队电子邮件时，PHP 7.3 和 Opcache 存在一些已知的错误，所以我鼓励你关注 [Github](https://github.com/laravel/framework/issues/26819) 上 Laravel 存储库中的问题。