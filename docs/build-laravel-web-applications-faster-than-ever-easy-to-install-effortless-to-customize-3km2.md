# 🛣构建 Laravel Web 应用程序的速度比以往任何时候都快，易于安装，轻松定制👋。

> 原文：<https://dev.to/getspooky/build-laravel-web-applications-faster-than-ever-easy-to-install-effortless-to-customize-3km2>

Laravel 是由 Taylor Otwell 构建的一个令人难以置信的框架，它结合了强大的 web 开发功能、丰富的文档和活跃的社区。在本教程中，我们将使用 laravelDash 构建一个带有管理面板的 fullstack 应用程序。

## 什么是 Laravel Dashboard？

开发者很懒。不，我不是在开玩笑——他们努力创建系统，帮助他们在未来避免更多的工作。尤其是重复性的工作。而且现在有相当极端的例子——我们不再需要写代码了；它是为我们而产生的。Laravel Dashboard 或 LaravelDash 为 Laravel 应用程序的 CRUD(创建、读取、更新、删除)操作提供了强大的用户界面。它提供了额外的功能，包括图表，面板管理，设置，支付系统和超级简单的所见即所得…

## 第 1 分钟:创建 Laravel 应用程序

我们假设您已经能够设置您的开发环境。安装 Laravel 项目有两种方法

*   通过 Laravel 安装程序
*   通过 Composer 创建-项目

我将使用 via composer 创建新项目

```
composer create-project --prefer-dist laravel/laravel blog 
```

Enter fullscreen mode Exit fullscreen mode

Laravel 提供了一种快速的方法，使用一个简单的命令:
来搭建认证所需的所有路由和视图

```
php artisan make:auth 
```

Enter fullscreen mode Exit fullscreen mode

## 第二分钟:配置

首先，确保创建一个新的数据库，并将您的数据库凭证添加到您的。环境文件:

```
APP_URL=http://localhost
DB_HOST=localhost
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret 
```

Enter fullscreen mode Exit fullscreen mode

## 第 3 分钟:安装并配置 laravelDash

LaravelDash 超级容易安装。在创建了新的 Laravel 应用程序之后，您可以使用下面的命令包含 laravelDash 包:

```
composer require yal/laraveldash 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 Laravel >=5.5，LaravelDash 将自动注册其服务提供商。如果您将 LaravelDash 与 Laravel 5.3 或 5.4 一起使用，请在应用程序的 config/app.php 文件中添加 LaravelDash 的服务提供者:

```
/*
 * Laravel dashboard Service Provider
 */
 \Yasser\LaravelDashboard\DashboardServiceProvider::class, 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要发布 laravelDash 配置文件:

```
php artisan vendor:publish --provider="Yasser\LaravelDashboard\DashboardServiceProvider" --tag="config" 
```

Enter fullscreen mode Exit fullscreen mode

## 第 4 分钟:运行迁移并定义关系

生成的迁移是常规的 Laravel 迁移，使用:
运行迁移

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

数据库表通常是相互关联的。例如，一篇博客文章可能有许多评论，或者一个订单可能与下订单的用户有关。雄辩使管理和处理这些关系变得容易。所以去 App\User.php 添加 UserRelation

```
<?php

namespace App;

use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Yasser\LaravelDashboard\Traits\UserRelation;

class User extends Authenticatable
{
    use Notifiable,UserRelation;

} 
```

Enter fullscreen mode Exit fullscreen mode

## 第 5 分钟:耶！你在 LaravelDash 上！

要查看它，您需要在您的开发机器上启动一个 web 服务器。您可以通过运行以下命令来实现这一点:php artisan serve，然后转到`http://localhost:8000/Dashboard`

无论您是在帮助我们修复错误、改进文档还是传播消息，我们都希望您成为`LaravelDash`社区的一员！💪💜参见 CONTRIBUTING.md，了解我们在寻找什么以及如何开始的更多信息。

GitHub:[https://github.com/getspooky/laravelDash](https://github.com/getspooky/laravelDash)