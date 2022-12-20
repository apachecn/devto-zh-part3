# 调试 Ruby on Rails

> 原文：<https://dev.to/justalever/debugging-ruby-on-rails-55jk>

Ruby on Rails 给了我们这么多现成的东西，但有时会出现错误，或者我们需要为某种类型的特定功能重新发明轮子。这个视频/文章是我自己调试 ruby on rails 的心得。

如果你以前使用 Ruby on Rails 开发过，你可能已经知道它是一个命令行驱动的环境，在这里你使用“生成器”在你的应用程序中创建一个元素。支持从创建数据库表到搭建动作视图的所有内容。关于 Rails CLI 的快速介绍，请查看我写的另一篇文章[了解 Ruby on Rails CLI](https://web-crunch.com/understanding-the-ruby-on-rails-cli/) 。

[https://www.youtube.com/embed/JnQ72lZeQyI](https://www.youtube.com/embed/JnQ72lZeQyI)

### Ruby on Rails 控制台(irb)

有了 irb 控制台环境，您可以将数据遍历到您的核心内容。访问它就像通过您选择的终端在您的应用程序中运行`rails console`一样简单。在那里，您可以创建各种各样的查询来检查您想要的数据类型和/或直接修改它。

一个有趣的技巧是在运行`rails console`时将标志`--sandbox`传递给它。这将对您的数据执行“假”更改，但最终会回滚您所操作的任何内容。这可能值得在生产环境中运行，以便在您确定将要提交的更改之前尝试检查错误。

### 测井

很明显，当你启动一个本地版本的 rails 应用程序时，你会看到一个活动列表，显示终端上发生的所有事情。当在前端执行操作时，这对于识别请求、参数等等有很大的帮助。在一个`POST`方法中，我经常使用日志来验证 stripe 支付数据，因为我需要这些参数传递给 stripe gem，并最终对我向用户出售或订阅的给定商品进行收费。

### `<%= debug(object) %>`法

当在前端视图上输出数据时，这是我的救命稻草。

假设我们有一个`@article`实例方法供我们使用。在显示视图中，我们可以添加以下内容:

```
<%= debug @article %>
<p>
  <b>Title:</b>
  <%= @article.title %>
</p> 
```

Enter fullscreen mode Exit fullscreen mode

这将输出类似如下的内容:

```
-------- !ruby/object Article
attributes:
  updated_at: 2008-09-05 22:55:47
  body: It's a very helpful guide for debugging your Rails app.
  title: Rails debugging guide
  published: t
  id: "1"
  created_at: 2008-09-05 22:55:47
attributes_cache: {}

Title: Rails debugging guide 
```

Enter fullscreen mode Exit fullscreen mode

`debug`将我们的实例方法呈现为`yaml`,这样在调试时可读性更好。

这对于你需要获取嵌套数据或记住字段名称的情况非常有用。我一直用这个。

### 网络控制台宝石

web 控制台 gem 是一个有用的工具，它会出现在你的 Rails 应用程序中的任何错误视图上。您可以在视图中添加`<% console %>`来显示控制台。加在哪里真的不重要。

如果您发现自己调试了很多，而宁愿在浏览器中调试，web-console 在您的视图中为您提供了一个完整的 CLI。我并没有大量使用它，但我确实对它在 Rails 应用程序中默认提供给那些可能需要它的人感到满意。最终，更少的配置是 Ruby on Rails 框架整体的精髓。

### byebug 宝石

bye bug gem 将调试提升到了一个全新的水平。可以把它看作是在出现错误时在控制器中添加断点的一种方式。Byebug 允许你一步一步地查看你的 ruby 代码在哪些地方进展顺利，哪些地方不顺利。默认情况下，这也存在于 Gemfile 中开发和测试组的 Rails 中。

上面的视频展示了它的功能。实际上，您可以根据命令暂停您的应用程序，以调试一些有问题的代码的给定实例。

### 撬宝石

[撬杆](https://rubygems.org/gems/pry-rails)宝石是你默认导轨`irb`控制台的有用替代品。Pry 给了你一堆探索你的 app(即`show-routes`、`show-models`等……)和语法高亮的方法。好奇你为什么要用撬吗？[查看这篇文章](http://lucapette.me/pry-everywhere)。

### 关闭

没有完美的调试方法。由于是基于惯例的，Rails 没有一个“定义好的”调试代码的方法。我认为这是一件好事，因为许多开发人员处理错误的方式不同。我是一个非常视觉化的人，所以在开发一个应用程序时，我经常接触`debug`助手和我的日志来找出哪里出错了(或对了)。把这些东西用在你的优势上！你做得越多，你就能越快地解决看似熟悉的未来问题。

### 无耻的塞时间

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

调试 Ruby on Rails 的帖子[最早出现在](https://web-crunch.com/debugging-ruby-on-rails/)[的 Web-Crunch](https://web-crunch.com) 上。