# 从 Rails 到 Elixir:使用 NOOP 部署的安全重写

> 原文：<https://dev.to/jdcosta/from-rails-to-elixir-safe-rewrites-with-noop-deployments-40cb>

这是我从 Ruby on Rails 到 Elixir/Phoenix 的旅程系列文章的第二篇。
*\Part-1 [从 Rails 到仙丹:了解你的应用](https://dev.to/jdcosta/from-rails-to-elixir-know-your-app-131m)*
*\Part-2 [从 Rails 到仙丹:用 NOOP 部署安全重写](https://dev.to/jdcosta/from-rails-to-elixir-safe-rewrites-with-noop-deployments-40cb)*

#### 重述

我正在将一个最初用 Ruby on Rails 构建的价格跟踪器应用程序移植到 Elixir/Phoenix。我的主要动机是降低基础设施成本，因为我目前每月支付 24 美元在谷歌云上购买一台虚拟机，这是支持整个系统的最小可能。请阅读[第 1 部分](https://dev.to/jdcosta/from-rails-to-elixir-know-your-app-131m/edit)，了解我为什么决定进行这次迁移的全面信息。

## 重构焦虑

能够更好地改进您现有的软件总是一件好事，而且常常是一种难得的特权。改进可能以不同的形式出现:您可以为较少测试的功能添加缺失的测试，通过升级依赖关系来更新和加强安全性，出于所有合理的原因重构一段代码，有时甚至将整个代码移植到更合适的技术，以增强灵活性/可靠性/性能。

然而，事情并不都是美好的。替换当前正在工作并服务于其目的的东西通常伴随着巨大的责任，必须交付一个至少和遗留东西一样好的替代品。还有转换的时刻，这需要仔细规划，部分关闭，在某些情况下，它甚至要求完全停止和重新启动整个系统。从来都不是好事。

这些大问题是:1)不希望在产品化阶段中断正在运行的系统，2)希望新的替代品能够交付预期的东西；是开发人员焦虑的两大来源。

## 重音少用 6 R 的

在我之前维护一个稳定增长的产品的经历中，我学到了很多关于在不影响正在进行的业务的情况下持续发展一个 5 年的代码库。作为一个小团队，我们不能承受由鲁莽的部署引起的生产中的意外失败，这将会转移每个人的注意力并有效地停止开发。

出于这个原因，我们总是非常认真地考虑如何将重构或任何变化投入生产。

我们使用的非正式技术根据即将到来的迁移的类型和规模而变化。它们通常属于 [6 应用程序迁移策略之一——由 Amazon AWS 在云环境中制定的 6r](https://aws.amazon.com/blogs/enterprise-strategy/6-strategies-for-migrating-applications-to-the-cloud/):

1\主机托管
2\ **平台重组**T3】3 \回购
4\重构(重新架构)
5\退役
6\保留

它们之间的界限很模糊，但我发现这个列表非常有用，可以清楚地定义我手头工作的界限。它让我思路清晰，并帮助我计划行动。比方说，我们正在将我们的基础设施从亚马逊 AWS 迁移到谷歌云——这就是**重新托管**,因此我们应该采取一切预防措施，将一个系统从一个地方迁移到另一个地方。这听起来很简单，也很让人放心。

我还建议一次只采用一种迁移策略。如果你需要从 AWS 迁移到 Google Cloud，也需要从 Ruby on Rails 迁移到 Elixir/Phoenix，那么一个接一个地做，不要试图同时做所有的事情。这可能很诱人，但如果你真的想一想，这实际上是在一个同样未知的环境中部署一个主要未知的软件。即使对试运行服务器进行了适当的测试，结果也是不可预测的。

根据我的经验，迁移成功与底层复杂性成反比。如果可以，确保您的迁移尽可能简单和统一。

## NOOP 部署

也许我最喜欢的技巧是我所谓的(*)和也许在我之前的某个人？*)NOOP 部署。

NOOP(无操作)是一个编程概念，用来描述一个实际上什么也不做的动作。它的起源可以追溯到汇编语言，在汇编语言中，它是一条在系统中不起作用的指令。[维基百科更了解](https://en.wikipedia.org/wiki/NOP_(code))。

那么到底什么是 NOOP 部署呢？这是一种部署技术，其中新代码被放置在它在产品中应该存在的地方，但是处于**非活动状态**。我将进一步扩展这一想法，将一个完全空白的 Elixir 应用程序部署到生产基础架构中，没有任何自定义代码，并让它在实现过程中保持惰性状态。

为了说明这个想法，下面的图片展示了当我不得不花费几周甚至几个月的时间从上到下开发一个应用程序，并最终交付生产时的感受:

[![moving house](img/34cf6198087b5d1817d585bc7dfee1d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pUaZsWIB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XKu6yxl.jpg%3F1)

好像不太对吧？你不知道新的地形是否能支撑建筑，也不知道旧的材料是否能在新的环境中发挥作用。现在想象一下，旁边还有其他房子，如果在你卸载旅行房屋的时候发生了灾难，你肯定不想把它们一起拖垮。这对你的邻里信誉没有任何好处。

这正是迁移出错的原因。将一个有缺陷的应用程序部署到生产基础设施中，靠近正在运行的系统的组件，一切都乱套了。想象一下数据库因连接突发、外部服务被请求轰炸或任何其他意外行为而变得疯狂。

为了我自己的理智，我更喜欢这样做:

[![house foundations](img/4e6a79eab6ae5d288c6145752fc1f0f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pVEt17Ly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4UUQE8X.jpg)

**找到地形，打好基础，在**的基础上进行建设。当开发注意到基础设施时，出错的机会就会少得多，当实现与部署同步进行时，好事就会出现。

[马丁·福勒](https://twitter.com/martinfowler)也基于这一想法，引入了[扼杀者应用](https://www.martinfowler.com/bliki/StranglerApplication.html)。这是一个受澳大利亚扼杀者藤蔓启发的概念，这种藤蔓“在无花果树的上部枝条中播种，并逐渐向下生长，直到扎根于土壤中”。这是对在旧系统边缘创建的子系统的一个很好的比喻，这些子系统逐渐成长，慢慢接管。

我将按照这种方法将我的 Ruby on Rails 应用程序移植到 Elixir/Phoenix。计划是生成一个空白的 Elixir 应用程序，并将其放在正在运行的 Rails 应用程序旁边。他们将共享相同的虚拟机，因为该项目的主要目标是保持现有服务器的精简版本。

## 仙丹部署的当前状态

我听过 José Valim 在一个[播客](https://elixiroutlaws.com/24)(大约 10:00 分钟)中谈论这种语言的未来，并说他仍然想解决的一件事是部署。他认为部署非常重要，它应该是语言本身的一个问题。

为了实现这一想法，他与造船厂的保罗·舍恩菲尔德(Paul Schoenfelder)和[酿酒厂的创建者](https://github.com/bitwalker/distillery)联手，以便统一来自社区的想法，在酿酒厂进行测试，并最终将一个经过打磨的版本整合到仙丹核心中。Distillery 是 Erlang 对 Elixir 的发布处理的重写，从一个真正的局外人的角度来看，它似乎是部署 Elixir 应用程序的最流行的解决方案之一。

Paul 撰写的这篇全面的文章解释了他们如何计划在 Distillery 2.0 中测试他们的想法，并在准备就绪后将它们移入 Elixir core。很明显，配置是他们的主要关注点，他们希望在它到达语言的核心之前就把它做好。

我看到了其他可用的替代方案，但老实说，它们没有像 Distillery 那样吸引我的注意，在了解到它的最新版本基本上是标准库中将要提供的内容的前身之后，我的注意就更少了。

## 生产发布用酒厂 2.0

在这一点上，我期待类似于 Capistrano 的东西，Ruby on Rails 人员的首选部署工具。将它添加为一个依赖项，生成几个文件，根据需要对其进行定制，然后运行一个命令将它发送到目的地。

乍一看，酿酒厂似乎非常完整。它有可爱的文档指导您完成安装过程，以及一些针对不同用例的指南。关于保护伞项目的那个派上了用场。

所以，把它热掉吧:

```
defp deps do
  [
    {:distillery, "~> 2.0"}
  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

安装并生成默认配置文件:

```
$ mix release.init 
```

Enter fullscreen mode Exit fullscreen mode

这给了我一个带有一些预定义值的`rel/config.exs`文件，可以根据需要进行定制。最重要的行可能是:

```
environment :prod do
  # ...
end

release :shopxir do
  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，它生成了一个与保护伞项目同名的版本(`shopxir`)，并且包含了迄今为止唯一的子应用程序- `jobs`。此子应用程序是一个空白的 Elixir 应用程序。里面什么都没有。

它还定义了两个环境，`dev`和`prod`。默认情况下，后者被配置为在生产版本中包含 Erlang 运行时，但不包含源代码。

最后，运行命令来构建发布版本:

```
$ MIX_ENV=prod mix release 
```

Enter fullscreen mode Exit fullscreen mode

这将用一堆文件和文件夹填充`_build/prod/`。这个命令有一个很好的输出，显示了一些可能的进一步操作，比如在后台启动，在前台启动，甚至是一种与正在运行的应用程序(`remote_console`)交互连接的方式。

为了确保我的版本已经打包好，我将向`jobs`子应用程序添加一个简单的静态模块，我可以在部署后调用它来验证它是否如预期的那样运行。

```
defmodule Ping do
  def ping do
    IO.puts "Pong."
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

重建并重新连接`remote_console` :

```
$ MIX_ENV=prod mix release
$ _build/prod/rel/shopxir/bin/shopxir start # in the background
$ _build/prod/rel/shopxir/bin/shopxir remote_console
...
iex(shopxir@127.0.0.1)1> Ping.ping
Pong.
:ok 
```

Enter fullscreen mode Exit fullscreen mode

我们开始了👍

我不打算讨论如何定制版本的细节，因为我对它们还不是很熟悉。随着实现的进行和新特性从 Rails 转移到 Elixir，我当然需要更深入地研究这个设置，但是我决定根据需要逐步进行。现在，我想要的只是一个放在服务器上的灵药应用程序。

## 部署

现在，Distillery 没有做的一件事是将新版本传输到生产基础架构的实际繁重工作，即终止正在运行的应用程序和初始化新版本。他们提供了一些基于 Docker 和 systemd 的指南，这还不错，但我一直在寻找一个更现成的工具。这是当我发现我是一个被宠坏的 Ruby kiddo👶

Docker 很酷，我在开发过程中经常在本地使用它来运行外部服务，如 PostgreSQL 和 Redis。与`docker-compose`一起，它使得组织每个项目的外部依赖变得容易。但是对于生产来说，感觉很难:配置管理、构建服务器、生成文件、编排、注册、发现、`Dockerfile`、`swarm`、`Kubernetes`、`etcd`...我已经累了。

如果生产部的 Docker 是你的事，请对我宽容些。我可能只需要适当地学习它。

另一方面，在 Ruby 世界里， [Capistrano](https://capistranorb.com/) 和它的插件为我做了一切。这个东西负责从 git 存储库中下载最新版本，在单独的文件夹中保存一组版本，编译资产，运行迁移，停止/(重新)启动应用程序和后台工作程序，通知监控服务等等，以一种非常简单的方式进行，几乎不需要任何配置。它也是高度可扩展的。

我想在长生不老药世界中找到类似的东西，但我没有找到如此完整和直接的东西。

幸运的是，有一个非常类似的工具叫做 [Ansistrano](https://github.com/ansistrano) 基于 [Ansible](https://www.ansible.com/) (供应框架)，顾名思义，它在 Capistrano 中受到了很大的启发。它符合我的需求，因为我已经在使用 Ansible 进行服务器配置了。

从几年前开始，Ansible 就一直是我所有项目中的常客。我主要将它用于基础设施配置，但它也可以用于部署。这是一个非常多才多艺的工具，人们用它来做许多与远程甚至本地机器操作相关的不同事情。

总之，Ansistrano 将执行以下操作:

1\使用 Distillery 在本地构建一个新版本
2\在远程服务器中创建一个文件夹，并将新版本复制到其中
3\符号链接新文件夹，以便它被解释为当前版本
4\创建/更新一个将执行应用程序的 systemd 服务
5\通过重新启动相应的 systemd 服务，切换到新版本

运行数据库迁移和资产编译等常见部署任务预计将使用 Distillery 钩子来处理，这就是为什么它们没有出现在前面的列表中。

**systemd** 在`foreground`模式下运行 app，默认发送`SIGTERM`终止。这对于现在来说已经足够了，因为没有正在运行的任务，比如由 Phoenix 处理的 HTTP 请求或其他在后台长时间运行的进程。

整个事情(包括模板文件)有 50 多行 Ansible 声明。在这个要点中检查它们[。](https://gist.github.com/joaodiogocosta/cde9f2973024975dc9b247afdaf56724)

## 总结&接下来

系统重写是困难的，也是开发人员焦虑的一大来源。它们需要仔细的计划和谨慎的方法。

通过简化迁移来减少焦虑和降低风险。使用诸如 6 R 之类的技术来创建焦点，使用 StranglerApplication 来逐步替换遗留系统，而不会造成损害。使用 NOOP 部署让您的第一次部署感到安全和放心。建立在。

Distillery 非常适合打包 Elixir 版本，但是其他工具如 Ansistrano 和 systemd 也必须在那里帮助远程服务器上的实际部署。

考虑到我是这方面的新手，我第一次部署 Elixir 应用程序的经历相当愉快。我仍然感觉到一些知识差距，我也很确定我当前的设置对于即将到来的事情来说太简单了，但是我很高兴从一个基本的方法开始，并根据需要进行扩展。

既然我已经在生产中部署了我的 Elixir 应用程序，那么是时候考虑应该首先迁移遗留 Rails 应用程序的哪些部分了。这将是一个重要的决定，我将在下一篇文章中讨论，但我的大脑已经在思考**背景工作**。

我希望你喜欢这个系列的第二篇文章。我试图通过删除一些代码片段和更深入地研究工具来更专业一点。我想知道你是否认为这是一件积极的事情。

一如既往，非常感谢您的反馈🙏🏻

感谢你阅读❤️