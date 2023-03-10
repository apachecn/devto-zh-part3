# 一个新的拉勒维尔项目需要考虑的五件事

> 原文：<https://dev.to/alchermd/five-things-to-consider-for-a-fresh-laravel-project-34e6>

<small>原发于我的[博客](https://johnalcher.me/articles/5-things-to-consider-fresh-laravel-project)T3】</small>

* * *

## 简介

在这篇短文中，我们将探讨一些你*可能*会为你的新 [Laravel](https://laravel.com) 项目考虑的事情。许多新开发人员直接从`laravel new myproject`开始写代码，没有任何想法或考虑，所以这里有一个列表，在你的第一个`git init`之后*可能会有用。我们开始吧！*

## 免责声明

以下只是**的意见**，不应该作为硬性规定来遵守。我建议你尝试一下，看看感觉是否合适，然后再做决定。你觉得这只会让事情变得复杂吗？恢复更改，顺其自然:)

### 1。将您的雄辩模型移入一个`Models`名称空间

默认的 Laravel 目录结构将您的大部分应用程序逻辑放置在`app`目录中，其中几个 [Artisan](https://laravel.com/docs/5.7/artisan) 命令将在预定义的目录/名称空间中为您生成文件。例如，`php artisan make:request SaveArticle`将创建一个名为`app/Requests/SaveArticle.php`的新文件，它的命名空间也在`App\Requests`下。

但是您的应用程序的模型如何呢？关于目录结构的[文档指出:](https://laravel.com/docs/5.7/structure#introduction)

> “当开始使用 Laravel 时，许多开发人员对缺少模型目录感到困惑。然而，缺少这样的目录是故意的。我们发现“模特”这个词有歧义，因为它对不同的人有不同的含义。一些开发人员将应用程序的“模型”称为其所有业务逻辑的总和，而其他人将“模型”称为与关系数据库交互的类……”

我得说，这很公平。但是就我个人而言，在参与了一系列 Laravel 项目之后，我发现雄辩的模型开始经常堵塞根目录。所以我建议从一开始(使用默认的`User`模型)，在`Models`目录下创建并移动你的雄辩模型。当您需要更多的分离时，您可以根据您的问题域轻松添加一个`Models\Billing`或`Models\Customers`。

### 2。使用内存中的 SQLite 数据库进行测试

我个人感觉这应该是 Laravel 的开箱即用配置。当您运行您的[测试](https://laravel.com/docs/5.7/testing)套件时， [PHPUnit](https://phpunit.de/) 将认可您的`phpunit.xml`的内容。对于一个新的 Laravel 项目，我首先改变的事情之一是使用一个*内存 SQLite 数据库*，而不是专门为测试创建一个传统的 RDBMS，如 MySQL 或 PostgreSQL。这将使测试执行得更快，如果您坚持使用标准的 SQL 命令，这对于运行 [CI](https://en.wikipedia.org/wiki/Continuous_integration) 测试来说是一个巨大的好处。

为此，您需要编辑您的`phpunit.xml`，在`<php>`块:
内设置`DB_CONNECTION`和`DB_DATABASE`

```
<php>
  <env name="DB_CONNECTION" value="sqlite"/>
  <env name="DB_DATABASE" value=":memory:"/>

  <!-- omitted for brevity... -->
</php> 
```

Enter fullscreen mode Exit fullscreen mode

### 3。安装望远镜进行开发

在设置和制作问题上挣扎了一会儿后，我终于适应并接受了将[拉弗尔望远镜](https://laravel.com/docs/5.7/telescope)作为我当前和未来项目的主要部分。这个小小的第一方工具给你的开发体验一个很好的提升。它允许您检查请求、呈现已发送的邮件、实时观察队列，等等。

只要确保仔细阅读[安装说明](https://laravel.com/docs/5.7/telescope#installation)，这样你就不会陷入奇怪的问题中。

### 4。更改应用程序名称

如果您正在构建一个旨在重用的包，这是一个更多的考虑因素，但是您可能希望更改您的应用程序名称，以便根据您的首选名称命名`app`目录，而不仅仅是`App`。有一个工匠命令可以做到这一点:

```
$ php artisan app:name Foo 
```

Enter fullscreen mode Exit fullscreen mode

这将命名您的`app`目录，因此`App\Providers`将成为`Foo\Providers`等。

### 5。更改前端预设

一个 web 应用程序在其生命周期中会做大量的前端工作。Laravel 为我们免费提供了一些优秀的前端工具，包括 [Laravel Mix](https://laravel.com/docs/5.7/mix) 和预生成的 [VueJS](https://vuejs.org/) 结构，它们可以在`resources/js`目录中找到。但是你知道吗，你可以很容易地用 [React](https://reactjs.org/) 替换这个预置，甚至完全删除这个预置。继续前进

```
# Swaps from Vue to React
$ php artisan preset react
# ... or remove the scaffold and go vanilla!
$ php artisan preset none 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们介绍了一些你可能会在新的 Laravel 项目中考虑的技术。同样，根据项目的需要，可以随意调整、删除或扩展这些想法。记住在项目的整个生命周期中做出明智的决定！

* * *

你有什么问题吗？欢迎随时在 Twitter 上联系我 [@alchermd](https://twitter.com/alchermd) 。下次见！