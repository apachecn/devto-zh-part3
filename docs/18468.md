# 我和露比在一起的头 12 个小时

> 原文：<https://dev.to/david_j_eddy/my-first-12-hours-with-ruby-145i>

Cross 从[我的博客](https://blog.davidjeddy.com/?p=1248&preview_id=1248)发布。

## 背景

在过去的 10 年左右的时间里，我一直是一名 PHP 开发人员。从 2000 年左右的 PHP 4 版本到 2018 年的当前版本 7。从脚本到面向对象，再到今天的现代语言生态系统。今年，我想学习一门新的语言，从不同的角度来解决问题。我试用了 Golang 几个月，因为并发和通道模型非常有趣，而且它是一种非常低级的语言(所有这些 PHP 都不是)。然而，最近我接触了一个目前用 Ruby(在 Rails 上)编写的项目。我想学习 Ruby 已经有一段时间了，所以这提供了一个极好的机会来尝试 Ruby/Rails。经过一周的业余辅导和阅读，这是我在第一个 12 小时后对 Ruby 的看法。

## 好的

**普通社区**

我在网上逛了逛。ICQ，AIM，Usenets，直拨 BBSs，栈溢出，Dev.to，区域会议等等。在我的经历中，我从未见过像 Ruby group 这样开放、友好和乐于助人的社区。如果我必须根据会员之间的友好程度来选择一门语言来学习；鲁比会轻而易举地获胜。不是说这个社区是完美的，只是总体上最好的。

**语言语法**

你知道，我喜欢简短的语法。它是基于点的语法(例如 JS)，paren 是可选的，数据类型几乎是自定义的。

```
[0 => 'value'] #array
{'key' => 'value'} #hash
1+2=3 # int + int = int
1.2 + 2.3 = 3.3 # dec + dec = dec
self.validate data #object.method parameter_date
```

可选择的父母一开始会感到困惑，但很快就在心里消化了。

**套餐管理**

经过一番挖掘，我对 Ruby 语言周围的生态系统很满意。依赖管理由[捆绑器](http://Bundler)和 [Gem](https://rubygems.org/) 系统很好地处理。虽然我个人并不喜欢 Gemfile 语法(作为一个 DSL ),但它可读性很强，很容易发现错误。我还喜欢它处理系统级的依赖性。需要 Postgres？补充一下。需要一个用于地理定位计算的库吗？补充一下。前端资产管道需要 SASS 流程吗？补充一下。甚至框架 Rails 和 Sinatra 都是你的项目的依赖项，而不是你的项目是从一个框架的特定提交中构建的。当然，有很多方法可以解决这个问题，但是 Ruby 是故意这样做的。

哦，还有 Ruby 作为类似 NodeJs 的版本管理器。叫做 RVM (Ruby 版本管理器)。

*   [https://bundler.io/](https://bundler.io/)
*   [https://www . cloud city . io/blog/2015/07/10/how-bundler-works-a-history-of-ruby-dependency-management/](https://www.cloudcity.io/blog/2015/07/10/how-bundler-works-a-history-of-ruby-dependency-management/)
*   [https://rvm.io/](https://rvm.io/)

## 严重的

**修改后的 SemVer**

出于对所有软件的热爱，请使用普通的服务器！这意味着你 Rub，Laravel，以及任何认为他们比近 60 年的发布管理做得更好的人。不要做一片特别的雪花。

**自动逻辑框架**

Rails 和 Sinatra 对于事情应该如何做都非常固执己见。想做的事情有点不一样？不，走开。这种语言非常灵活，但是最上面的两个框架非常固执己见。我想这与约定胜于配置密切相关。为了让约定正常工作，你必须期望事情是既定的。

## 丑陋的

**语法可能有点太短了**

```
[A] - [B] = [C]
```

这就是 Ruby 中的数组减法。如果您不知道这些是数组，那么您知道发生了什么的可能性很小。对数字数学有意义，但是数字通常没有括号。#困惑有些语言有用于构造操作的静态函数。不是露比，不是。“万物皆物”，所以万物都有先天的能力和功能性。我知道，“不可能每天都让每个人满意”。这实在是太吹毛求疵了。

**性能**

这是露比倒下的地方。对于所有的短语法，一切都是一个对象，整齐一致；在二叉树测试中，Ruby 2.6 落后于 PHP 74.3。[https://benchmarks game-team . pages . debian . net/benchmarks game/faster/yarv-PHP . html](https://benchmarksgame-team.pages.debian.net/benchmarksgame/faster/yarv-php.html)
随你怎么说，但在即时信息的时代，每 10ms 都很重要。在一些例子中，Ruby 是冰河时期缓慢的。解决这个问题的简单方法是缓存(Redis 等)。它甚至给最基本的应用程序增加了一层复杂性，但通常是任何规模的应用程序都需要的。

## 关闭

到目前为止，我很喜欢它。我可以想象自己使用 Ruby (+Rails / Sinatra)开发自动化脚本的快速 web 项目，就像使用 PHP 或 Golang 一样容易。0 分(差)到 10 分(好)我这次给 Ruby 打了 8.5 分。肯定会坚持下去，但也有成长的空间。

你呢，你有没有长时间使用 Ruby？你对 Ruby 有什么感受或者看法吗？在学习语言的过程中，你发现了什么有用的资源吗？请在下面的评论中分享。