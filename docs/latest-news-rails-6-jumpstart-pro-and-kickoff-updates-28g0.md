# 最新新闻、Rails 6、Jumpstart Pro 和启动更新

> 原文：<https://dev.to/justalever/latest-news-rails-6-jumpstart-pro-and-kickoff-updates-28g0>

我一直在忙着让我的新课程[hello ails . io](https://hellorails.io)更好地准备在本周推出。在这样做的同时，最近宣布 Rails 6 的第一个候选版本已经出来了。我花了一些时间谈论新闻、我的 ruby on rails 入门模板的更新以及我与一些朋友合作的另一个项目。

[https://www.youtube.com/embed/0IR5Vt7u2vQ](https://www.youtube.com/embed/0IR5Vt7u2vQ)

### 有什么新鲜事？

如果你还不知道，我开始创建一个名为 **Hello Rails** 的课程。我发现我在 YouTube 上的许多与 Ruby on Rails 相关的视频/系列都得到了很多关注。我收到了很多关于如何使用框架完成某些事情的评论/问题。从那以后，我产生了一个想法，在从零开始学习 Ruby on Rails 方面，我做了一些比其他任何事情都更彻底的事情。把我的课程当作一个切入点，让那些希望了解更多关于 Ruby on Rails 框架的知识，开始构建 web 应用程序，或者开发人员过渡到一个新的工具/技能集，以增加他们的武器库。

我的方法相当彻底，这与我在野外看到的许多教程的内容相反。有些视频可能会很长，但这是因为我不会跳过任何重要的片段。如果你决定学习这门课程，我们将一起模仿 Reddit 构建一个完整的 web 应用程序。我们将从头开始构建一个可发布的应用程序，并进行部署。

目前我正处于编辑阶段。在过去的几个月里，我录制了超过 90 个视频的 27 个模块。当我写这篇文章的时候，我已经完成了一半的视频编辑工作。我的希望/目标是在本月晚些时候或下个月初开设这门课程(不敢保证！).走到这一步是相当努力的，我很兴奋它即将准备就绪。如果你想在发布时得到通知，请务必在[hello ails . io](https://hellorails.io)订阅。

### 铁轨 6

[Rails 6.0.0.rc1](https://weblog.rubyonrails.org/2019/4/24/Rails-6-0-rc1-released/) 出了！我使用 Rails 6 已经有一段时间了，非常喜欢即将推出的更新。当涉及到构建 web 应用程序时，有一些主要的和一些小的附加功能可以真正扩展您的视野。点击了解更多关于[的最新消息。](https://weblog.rubyonrails.org/2019/1/18/Rails-6-0-Action-Mailbox-Action-Text-Multiple-DBs-Parallel-Testing/)

### 启动–顺风(Rails 应用程序模板)

鉴于新的发布候选，我决定更新我的[启动-顺风应用](https://github.com/justalever/kickoff_tailwind)模板以适应。

我删除了一些内容，更新了其他内容。总而言之，它非常小，因为 Rails 6 现在默认带有 Webpacker。一个大开关是 Tailwind CSS v1 的更新版本(目前仍在测试中)。

要使用模板，请务必参考视频。您需要在您选择的 Ruby 环境管理器中安装 Rails 的测试版。在写作的这个时候应该会成功。也可以运行`gem install rails -v 6.0.0.rc1`直接安装。我发现，首先创建一个普通的 rails 应用程序，然后使用我的应用程序模板创建另一个应用程序，效果最好。在运行通过的模板之前，需要安装某些 gem。

```
$ rails new vanilla_rails_app # just to get a proof of concept on Rails -v 6.0.0.rc1

$ rails new myapp -m template.rb # using my template (be sure to be inside the template directory when running this) 
```

Enter fullscreen mode Exit fullscreen mode

### Jumpstart Pro

毫无疑问，我上面的应用程序模板很大程度上受到了 [Chris Oliver 的](https://twitter.com/excid3/) [Jumpstart](https://github.com/excid3/jumpstart) Rails 应用程序模板的启发。克里斯在他的模板上得到了很多眼睛，他用它来截屏 gorails.com。原来我们住得很近，并开始与 Jason Charnes 合作一个新项目 Jumpstart Pro。

考虑一下 Jumpstart Pro 吧，它是一个更加精细的解决方案，可以让 Ruby on Rails 应用程序几乎不费吹灰之力地配置成默认状态。应用程序模板附带了对许多特性的支持，并且还会添加更多特性。我们有一个登录页面，最终将容纳文档和获取模板的方法。它目前仍处于早期测试阶段，但可以免费订阅，在 https://jumpstartrails.com 了解更多信息。

发生了很多事！我很高兴能推出我的课程和 Jumpstart Pro，并为博客/ [YouTube 频道](https://youtube.com/c/webcrunch)创建更多 Rails 内容。许多未来的计划包括深入探究 Ruby on Rails 框架中难以理解的主题。我最终想学习并享受在公共场合这样做，因为这也能帮助别人。

### 无耻的塞时间

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[最新消息、Rails 6、Jumpstart Pro 和启动更新](https://web-crunch.com/latest-news-rails-6-jumpstart-pro-kickoff-updates/)首先出现在 [Web-Crunch](https://web-crunch.com) 上。