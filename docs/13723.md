# 了解 Ruby on Rails CLI

> 原文：<https://dev.to/justalever/understanding-the-ruby-on-rails-cli-1n9h>

Ruby on Rails 就是这样一个庞大的框架。Ruby on Rails CLI 使得使用框架变得更加容易。这个视频/文章是我自己在学习框架的过程中历经磨难的结果。

初次创建新应用程序时，理解 Rails CLI 非常有用。一旦你安装了 Rails，你就可以打开一个新的终端窗口并输入`rails`。在那里，将出现一个大的选项列表，帮助指导您首次设置应用程序。可用的选项几乎是无穷无尽的，这使得配置您的应用程序如同在公园散步。

[https://www.youtube.com/embed/Ps9n8Evgxlw](https://www.youtube.com/embed/Ps9n8Evgxlw)

在我的视频示例中，我通过运行:
创建了一个新的 ruby on rails 应用程序

```
$ rails new demo_api --skip-coffee -T --webpack=vue --api 
```

Enter fullscreen mode Exit fullscreen mode

这将搭建一个新的应用程序，同时进行以下更改:

*   `--skip-coffee` -跳过使用`CoffeeScript`而选择常规的`javascript`。
*   当一个模型或者资源被生成时，跳过添加任何新的测试。
*   `--webpack=vue` -这告诉 rails 利用 webpack(如果你需要的话，它现在是内置的)并且安装/配置`Vue.js`前端 javascript 框架
*   `--api` -仅将新应用程序配置为 API，以便每个控制器呈现`json`，并且除了邮件程序之外没有视图。

虽然这个列表是一个很好的开始，但是你可以传递更多的选项来增强或“去除”你的应用程序，这取决于你想要构建什么。

#### 新申请内

创建好新应用程序后，您可以再次在根应用程序目录中单独运行`rails`。这提供了另一个长长的可用命令列表。我在视频中创建的演示应用程序如下。这里有许多创建命令，这使得我们作为 ruby on rails 开发人员的生活变得更加容易。我邀请您更多地了解他们，这样您的工作流程会更快。

```
The most common rails commands are:
 generate     Generate new code (short-cut alias: "g")
 console      Start the Rails console (short-cut alias: "c")
 server       Start the Rails server (short-cut alias: "s")
 test Run tests except system tests (short-cut alias: "t")
 test:system  Run system tests
 dbconsole    Start a console for the database specified in config/database.yml
              (short-cut alias: "db")

 new          Create a new Rails application. "rails new my_app" creates a
              new application called MyApp in "./my_app"

All commands can be run with -h (or --help) for more information.
In addition to those commands, there are:

Rails:
  console
  credentials:edit
  credentials:show
  dbconsole
  destroy
  encrypted:edit
  encrypted:show
  generate
  new
  runner
  secrets:edit
  secrets:setup
  secrets:show
  server
  test version

Rake:
  about
  active_storage:install
  app:template
  app:update
  db:create
  db:drop
  db:environment:set
  db:fixtures:load
  db:migrate
  db:migrate:status
  db:rollback
  db:schema:cache:clear
  db:schema:cache:dump
  db:schema:dump
  db:schema:load
  db:seed
  db:setup
  db:structure:dump
  db:structure:load
  db:version
  dev:cache
  initializers
  log:clear
  middleware
  notes
  notes:custom
  restart
  routes
  secret
  stats
  time:zones[country_or_offset]
  tmp:clear
  tmp:create
  webpacker
  webpacker:binstubs
  webpacker:check_binstubs
  webpacker:check_node
  webpacker:check_yarn
  webpacker:clobber
  webpacker:compile
  webpacker:info
  webpacker:install
  webpacker:install:angular
  webpacker:install:coffee
  webpacker:install:elm
  webpacker:install:erb
  webpacker:install:react
  webpacker:install:stimulus
  webpacker:install:typescript
  webpacker:install:vue
  webpacker:verify_install
  webpacker:yarn_install
  yarn:install 
```

Enter fullscreen mode Exit fullscreen mode

### 关闭思绪

当我第一次开始使用 Ruby on Rails 时，内置于其中的功能对我来说太令人畏惧了。随着时间的推移，在开发了一些应用程序后，我开始喜欢它所提供的东西。使用命令行工具可以快速配置、生成、删除等等。如果你是 Ruby on Rails 的新手，你应该在一个测试项目中摆弄一下 CLI，看看你能想到什么。打碎东西，然后重新组装。这是最好的学习方法。

#### 相关视频

*   【Ruby on Rails 应用模板使用指南

## 不要脸的插头

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多内容。我计划在这里开始创作更多的作品。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

 [![](img/c009079376aa2fccf2abd73f5ab135d8.png)](https://hellorails.io) 
☝想从头开始学习 Ruby on Rails？看看我即将推出的课程 [Hello Rails](https://hellorails.io)