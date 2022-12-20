# Ruby on Rails 应用程序模板使用指南

> 原文：<https://dev.to/justalever/a-guide-to-using-ruby-on-rails-application-templates-12jh>

[https://www.youtube.com/embed/kuKMRl8nj2w](https://www.youtube.com/embed/kuKMRl8nj2w)

随着我对 Ruby on Rails 的经验不断增长，我越来越多地探索如何为新项目提供一个基础应用程序模板。在本指南中，我将讨论我是如何创建最新的 rails 应用程序模板的，以及都包含了哪些内容。

据我所知，Ruby on Rails 是通往值得生产的应用程序的最快途径。您可以使用内置的 CLI 在短短 5 分钟内生成一个完整的博客。考虑到这一点，以及我不断努力提供有关该框架的教程，我开始构建一个应用程序模板以供将来使用。

我的 Let's Build: With Ruby on Rails 系列是我学习框架的基础。在几次构建之后，我开始注意到在每次运行`rails new`时，我需要配置一个新应用程序的方式中包含了很多模式。

由于教程的性质，我想提供“从零开始的方法”，因为许多教程都假设你在开始之前已经知道了很多。我仍然相信这个原则，因为熟能生巧，但在我的特殊情况下，我利用应用程序模板绕过了许多基本的配置。

使用应用程序模板，您可以创建一个新的 Rails 应用程序，并根据您在过去/当前项目中发现的最常用的内容来定义预配置的决策。假设您需要使用模板，这可以节省大量时间。

如果你真的没有构建大量的应用程序，这可能不适合你，但如果你有最终想要构建的想法，并且不想在每次启动时浪费时间配置应用程序，这可能是值得的。

### 什么是 Ruby on Rails 应用程序模板？

> 应用程序模板是简单的 Ruby 文件，包含用于添加 gems 初始化器等的 DSL。添加到新创建的 Rails 项目或现有的 Rails 项目中。

简而言之，这是一种使用一个命令来安装您喜欢的 gems、配置您的应用程序，甚至版本控制您的项目的方法。

### 如何使用模板？

如果你是 Ruby on Rails 的新手，我邀请你看看我在这篇文章底部链接的系列文章。如果你对这个框架有所了解，你现在可能知道创建一个新的应用程序就像运行`rails new my_app`一样简单。

传递模板的工作方式是这样的

```
$ rails new my_app -m template.rb 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们的 CLI 将寻找一个名为`template.rb`的文件来引导它安装一个新的应用程序。

你可以[下载我用](https://github.com/justalever/kickoff_tailwind) [Tailwind CSS](https://tailwindcss.com/) 创建的最新模板。我把它叫做`kickoff_tailwind`。

在文件夹中下载该文件和`cd`的副本。在那里，你可以像我之前所说的那样，通过传递`template.rb`文件来创建你的应用程序。

从那里你的应用程序将搭建起来，你可以将应用程序文件夹移动到你系统上任何你喜欢的地方。

### 附带什么？

包括的宝石有:

*   [设计](https://github.com/plataformatec/devise)
*   [friendly_id](https://github.com/norman/friendly_id)
*   [领班](https://github.com/ddollar/foreman)
*   [sidekiq](https://github.com/mperham/sidekiq)
*   [tailwindcss](https://github.com/IcaliaLabs/tailwindcss-rails)
*   [网络打包机](https://github.com/rails/webpacker)

*   Webpack 支持+在`app/javascript`目录下配置的 Tailwind CSS。

*   使用已迁移的新用户名和名称字段进行设计。使用顺风 CSS 增强视图。

*   由于方便的 friendly_id gem，支持友好 id。请注意，您仍然需要在您的模型内部做一些工作，这样才能工作。该模板安装 gem 并运行相关的生成器。

*   领班的支持多亏了一个侧面。一旦搭建好模板，运行`foreman start`进行初始化，然后运行`locahost:5000`让`rails server`、`sidekiq`和`webpack-dev-server`在一个终端实例中运行。注意:如果你不想使用 Foreman，Webpack 仍然可以在 rails 服务器上编译。

*   生成主题资源时的自定义 scaffold 视图模板(正在进行中)。

*   开箱即用的 Git 初始化

#### 更多模板

*   [开球顺风](https://github.com/justalever/kickoff_tailwind)

*   [开球-布尔玛](https://github.com/justalever/kickoff)

*   [Jumpstart - Bootstrap](https://github.com/excid3/jumpstart) -来自 gorails.com[的 Chris Oliver](https://gorails.com)-一个巨大的开球灵感

## 不要脸的插头

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多内容。我计划在这里开始创作更多的作品。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

 [![](img/c009079376aa2fccf2abd73f5ab135d8.png)](https://hellorails.io) 
☝想从头开始学习 Ruby on Rails？看看我即将推出的课程 [Hello Rails](https://hellorails.io)