# Ruby 中的调试——破解链轮中一年的 Bug

> 原文：<https://dev.to/heroku/debugging-in-rubybusting-a-year-old-bug-in-sprockets-42ki>

调试是一项重要的技能，在您开发更复杂的项目时需要不断提高。经验丰富的工程师对消灭 bug 有第六感，并建立了一个令人印象深刻的工具集，帮助他们诊断和修复 bug。

我是 Heroku 的 Ruby 团队的一员，也是 [CodeTriage](https://www.codetriage.com/) 的创建者，今天我们来看看我在修复[链轮](https://github.com/rails/sprockets)中一个严重缺陷的过程中使用的工具。链轮是用 Ruby 编写的资产打包系统，位于 Rails 资产处理管道的核心。

在这篇文章的最后，你会知道链轮如何工作，以及如何在 Ruby 中调试。

# 链轮中的意外行为

链轮为开发人员提供了一种方便的方式来编译、缩小和提供 JavaScript 和 CSS 文件。它的可扩展预处理器管道支持 CoffeeScript、SaaS 和 SCSS 等语言。它通过[链轮-轨道](https://github.com/rails/sprockets-rails) gem 包含在 Rails 中，但也可以独立使用，例如[打包 Sinatra 资产](http://recipes.sinatrarb.com/p/asset_management/sprockets)。

本月早些时候，我们记录了一个[现场调试会议](https://www.youtube.com/watch?v=ZEoF_OWpXZY&feature=youtu.be)，在那里我们遇到了链轮中特别奇怪的问题。我们注意到这个 bug 中断了关键资产预编译 rake 任务，但是只有当项目文件夹的名称在连续的任务执行之间被改变时。虽然项目文件夹重命名看起来相对不常见，但在 Heroku 上经常发生，因为每个构建都发生在一个唯一的目录名中。

虽然这个 bug 本身很有趣，但更有趣的是从我们的调试过程中学习。您可以了解我们用来缩小根本原因并最终修复 bug 的工具和步骤。

如果你想看完整的调试过程，可以看看视频或者阅读下面的文字。我们将遍历一个调试工作流，并找到这个 bug 的根本原因。

[https://www.youtube.com/embed/ZEoF_OWpXZY](https://www.youtube.com/embed/ZEoF_OWpXZY)

# Ruby 调试指南

令人挠头的、不明显的 bug 值得调查，因为它们可能会导致其他未被注意到或未报告的 bug。

幸运的是，Ruby 附带了一些强大的调试工具，对于初学者来说很容易使用。为了更好的概述，请查看这个 [Ruby 调试指南](https://www.rubyguides.com/2015/07/ruby-debugging/)，它涵盖了基本知识，比如`p`和`puts`之间的区别，还讨论了 Ruby 生态系统中可用的一些交互式调试器。然而，在这篇文章的其余部分，你不需要知道比`puts`更高级的东西。

# 复制 Bug

学习调试的最好方法就是一头扎进去试一试。让我们在本地环境中设置链轮。

## 克隆分诊

我们需要一个 Rails 应用程序来重现这个错误，所以我们将使用一个开源示例。我是 CodeTriage 的创建者，所以使用该应用程序来演示这个问题是很自然的，尽管您可以用任何使用链轮的 Rails 应用程序来重现它。CodeTriage 已经帮助开发者为成千上万的开源项目筛选问题。

首先，克隆 CodeTriage 存储库，安装依赖项，然后切换到包含重现 bug 所需代码的分支。假设有一个工作的 Ruby 环境。

```
$ git clone git@github.com:codetriage/codetriage
$ cd codetriage

$ gem install bundler
$ bundle install

$ cp config/database.example.yml config/database.yml
$ git checkout 52d57d13 
```

## 用 Rake 编译资产

接下来，执行以下步骤，让 bug 出现在我们的本地环境中。

```
$ rm -rf tmp/cache
$ rm -rf public/assets 
```

接下来，运行预编译资产的 rake 任务，应该会成功:

```
$ RAILS_ENV=production RAILS_SERVE_STATIC_FILES=1 RAILS_LOG_TO_STDOUT=1 bin/rake assets:precompile 
```

## 重命名项目文件夹

现在，通过将项目目录的文件复制到名为`codetriage-after`的新目录并删除旧的`codetriage`目录来更改项目目录的名称。

```
$ cd ..
$ cp -r codetriage codetriage-after
$ rm -rf codetriage
$ cd codetriage-after 
```

再次运行`assets:precompile` rake 任务:

```
$ RAILS_ENV=production RAILS_SERVE_STATIC_FILES=1 RAILS_LOG_TO_STDOUT=1 bin/rake assets:precompile 
```

这次任务应该会失败，并产生以下错误消息:

[![Screen Shot 2019-02-26 at 10](img/5a5c3bca8e8b2c5acceac79d45a84847.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wf2LMw4x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1551205073-Screen%2520Shot%25202019-02-26%2520at%252010.17.16%2520AM.png)

链轮抱怨它找不到文件`/private/tmp/repro/codetriage/app/assets/javascripts/application.js.erb`。

这实际上是有意义的，因为在最后一步中我们将`codetriage`改为`codetriage-after`作为我们的项目文件夹名称，然而它是在`codetriage`中查找的。

(注意，路径的`/private/tmp/repro`部分可能因您克隆`codetriage`库的位置而异。)

# 寻找 Bug 的根源

既然我们已经重现了视频中的 bug，下一步就是在 stack trace 中的某一行用一个叫做`fetch_asset_from_dependency_cache`的方法跳转到链轮依赖关系的代码中。您的应用程序依赖于读取库中的代码，这在调试时是必需的，尤其是在您已经排除了您编写的代码的任何问题之后。

## 打开包读取 gem 代码

Ruby 事实上的 gem 管理器 [Bundler](https://bundler.io/) 包含一个叫做`bundle open`的有用命令，可以在你最喜欢的编辑器中打开 gem 的源代码。这样运行:

```
$ bundle open sprockets 
```

只要您设置了一个`$EDITOR`或`$BUNDLER_EDITOR`环境变量，您的首选代码编辑器就会打开指定 gem 的项目目录。

现在，您可以浏览 gem 源代码，甚至修改它，添加打印语句来查看变量的值，或者尝试各种修复来查看它们是否有效。

## 链轮如何缓存文件

上面的错误消息暗示链轮缓存中存储了错误的值，因此下一步是查看缓存进行确认。缓存跨许多文件存储在磁盘上，所以首先我们需要找到包含我们想要检查的记录的特定文件。该记录的关键是链轮缓存 ID 的摘要。这就是我们试图在文件中找到的价值。

一旦你打开了链轮代码，导航到`lib/sprockets/loader.rb`，在这里你会找到方法`fetch_asset_from_dependency_cache`的末尾。该方法的文档提供了关于链轮如何使用管道、历史和依赖关系的概念来帮助缓存的见解。为了获得更多的背景故事，我建议从大约六分钟开始观看视频。

我们检查了链轮缓存的磁盘内容，在链轮缓存中寻找特定对象的 ID 缓存键。

```
$ grep -R 5d0abb0a8654a1f03d6b27 tmp/cache 
```

这是一个有用的调试命令，可以存档以备后用。`grep -R`在`tmp/cache`目录中搜索任何包含字符串“5d0abb0a8654a1f03d6b27”的文件，该字符串是链轮缓存关键字。R 标志使它递归地遍历目录。

在我们的例子中，grep 命令确实产生了一个缓存文件，我们可以使用`cat`来查看内容。在缓存文件内部，我们发现了意想不到的东西:资产的绝对路径。链轮应该只缓存相对路径，而不是绝对路径。由于我们更改了项目目录的绝对路径来创建这个 bug，很可能这就是罪魁祸首。

## 加载 IRB

为了进一步调查并证实他的怀疑，我们启动了交互式 Ruby 调试器 IRB。如果你是 Ruby 或 IRB 的新手，我们推荐[如何使用 IRB 来探索 Ruby](https://www.digitalocean.com/community/tutorials/how-to-use-irb-to-explore-ruby) ，这是了解如何使用它的好方法。它简单但功能强大，是 Ruby 调试工具包中的必备工具。

然后，我们使用 IRB 从链轮的角度检查文件缓存。

```
$ irb
reirb(main):001:0> require ‘sprockets’
reirb(main):001:0> Sprockets::Environment.new.cache
reirb(main):001:0> Sprockets::Environment.new.cache.get(“5d0abb0a8654a1f03d6b27”) 
```

不幸的是，这不起作用，因为缓存键与缓存 ID 不同。所以，我们继续用另一种方式来证实他的假设。我们仍然在这里包含这个例子，让您知道 IRB 可以用于任何 Ruby 代码，特别是链轮中的 handy Environment 类。

## 固定 to_load 和 to_link

为了修复这个 bug，让我们修改`loader.rb`中的`to_load`和`to_link`方法，使用链轮`base.rb`中的`compress_from_root`和`expand_from_root`实用程序方法，强制对象进入和离开缓存的相对路径。这确保了绝对路径不会再次进入缓存，因此，重命名项目目录不会导致后续资产编译中的任何问题。

```
if cached_asset[:metadata][:to_load] && !cached_asset[:metadata][:to_load].empty?
  cached_asset[:metadata][:to_load] = cached_asset[:metadata][:to_load].dup
  cached_asset[:metadata][:to_load].map! { |uri| compress_from_root(uri) }
end

 if cached_asset[:metadata][:to_link] && !cached_asset[:metadata][:to_link].empty?
  cached_asset[:metadata][:to_link] = cached_asset[:metadata][:to_link].dup
  cached_asset[:metadata][:to_link].map! { |uri| compress_from_root(uri) }
end 
```

我们修复 bug 的 [pull 请求包含一个测试来证明修复是有效的。为 bug 修复编写测试是您应该始终努力遵循的最佳实践。这是防止老 bug 爬回你的代码库的最好方法。](https://github.com/rails/sprockets/pull/547/files)

# 总结

不可避免地，你的代码会做一些不可能发生的事情。这时你需要拿出你的调试工具。我们希望你能从这篇文章中获得一些新的东西。

你的代码会做一些在生产中不可能发生的事情。如果你的应用运行在 Heroku 上，确保你熟悉各种各样的[日志解决方案，这些解决方案可以作为附加组件](https://devcenter.heroku.com/articles/logging)使用。这些附加组件将使在 Heroku 上运行和调试问题变得更加容易，并且只需要几秒钟就可以设置好。