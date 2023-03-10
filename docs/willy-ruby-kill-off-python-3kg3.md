# Ruby 和 Python，它们和它们的区别

> 原文：<https://dev.to/rapidnerd/willy-ruby-kill-off-python-3kg3>

Ruby 和 Python 被认为是每天都在持续增长的最常用和最流行的两种语言。两者都定期更新，如果你给它命名，那么很可能会有一个库、API 或其他为你所用的东西。但是随着 Ruby 的增长，它的用户数量也在增长，Ruby 会比 Python 更受欢迎吗？

快速披露:我将着眼于每种语言的一些主要方面，但不会着眼于 API、库等，除非它是特定于该主题的。

# 历史

在比较任何事情之前，我相信回顾历史总是很重要的。Guido Van Rossum 在 1991 年发明的 Python 和 Yukihiro Matsumoto 在 1995 年发明的 Ruby 现在是世界上十大语言中的两个。Ruby 在一些你最喜欢的网站上被广泛使用，Twitter，Github，Airbnb，甚至 Dev.to. Python，也在 Google，YouTube 和 Dropbox 上使用。毫无疑问，自从他们成立以来，他们已经改变了开发行业。

# 语法

我们都知道语言的语法对于程序员来说是必不可少的，有人喜欢 Java 语法，有人喜欢 Python heck 有人喜欢 Brainfuck。

Ruby 被设计成一种灵活且强大的语言，然而这种灵活性也会带来一些问题。Ruby 在你意想不到的时候变得如此神奇，这也让你在梳理头发的时候花上几个小时去追踪 bug、梳理代码变得很痛苦。

Ruby 的语法示例

```
def hello_world()
    puts 'Hello world'
    name = gets.chomp
    puts = "Hi #{name}"
end 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，Python 被设计成一种更简单的方法，用缩进代替花括号或关键字。目标是让一切对开发者可见，在某种意义上，这牺牲了 Ruby 必须提供的一些优雅。

Python 的语法示例

```
def hello_world()
    print('Hello world')
    name = input('Enter your name')
    print(f'Hello {name}') 
```

Enter fullscreen mode Exit fullscreen mode

# 网页开发

鉴于 Dev.to 有很多 web 开发人员，不谈论 web 开发是愚蠢的。这两种语言都有两个主要的框架。到目前为止，Ruby rails 是最受欢迎的，Python 有两个，取决于最终目标是什么，它们是 Django 和 Flask。为此，我将使用 Django 作为比较。让我们来看看利弊...这在某些地区很难确定。

#### 滑轨优点

1.  更快地启动新项目
2.  让一切发生的代码更少
3.  更大的 rails 社区
4.  更轻松的迁移
5.  非常易于部署

#### 轨道构造

1.  会变得臃肿——Rails 捆绑了一些不必要的代码，不太资深的开发人员可能不理解它
2.  如果你需要捆绑数据分析、大数据功能，那么 Ruby 就不是合适的选择
3.  要遵循的代码约定更少，有时代码可以工作，但很难知道为什么

#### 姜戈的优点

1.  更严格的语法
2.  出厂时已在安装时预制了全面的门户网站
3.  强大的中间件层
4.  django rest 框架是最好的框架之一
5.  可扩展缓存系统

#### 姜戈 cons

1.  默认情况下不是异步的
2.  对于较小的项目来说，真的是大材小用
3.  所有东西一起部署

我不会评论哪一个更好，因为我是 Rails/Ruby 的新手，而不是 Django/Python，但是从研究两者来看，它们显然各有优缺点。如果你正在寻找一个网站可以使用的东西，那么这两种都非常适合你的网站，但是总是取决于网站的需求。

# 社区

Ruby 和 Python 背后都有大量的社区，论坛、slack 组、discord 服务器等等，无论你在哪里看，你都会发现一个活跃的社区在这里帮助和指导你。每一个社区都受到这种语言及其背后的软件的影响。

Python 拥有比 Ruby 更广泛的社区。这两种语言在数学、科学、编程等领域都有大量的学术用例。但这并不意味着 Rubys 社区停滞不前，它是一门每天都在发展的语言。

Ruby 的流行真正开始于 2005 年 Rails 的出现，社区爱上了它，并且非常关注 web 开发。此外，它的多样性仍在快速增长。

# 网络安全/钢笔测试

Python 和 Ruby 都被大量用于网络安全、sec ops 和 pen 测试，背后都有庞大的社区。无论你在哪里寻找这个主题，你都会找到你想要的，例如 Ruby 拥有由 Rapid7 开发的 Metasploit，这是使用 Python 进行 pen 测试最常用的工具之一，拥有 Nmap 和 Scapy。都是日常开发和使用的。

#### Python 的优点

1.  高性能
2.  广泛的支持
3.  无论你看哪里，你都会找到你需要的东西
4.  用户友好的数据结构

#### Python 弊

1.  将库和工具捆绑在一起可能会很麻烦
2.  一些库/工具是付费的
3.  通常特定于 Python 版本

#### 红宝石优点

1.  不错的语法
2.  许多需要的东西都内置在标准库和语言中(可变字符串、碎片等)
3.  高度可扩展

#### 红宝石弊

1.  较小的社区
2.  不再维护某些必需的库/API
3.  一些公司不使用它，所以如果你是唯一一个进去的，祝你好运。

# 套餐管理

Python 和 Ruby 都有令人难以置信的包管理器。对于 Rubys RubyGem 和 Bundlr 来说，它们都没有问题，对于 Python 来说，拥有 Pip(3)和 Anaconda，它们在安装、维护和查找所需的包方面都做得非常出色。无论你看哪种语言，你都不会对它们失望。

# 个人见解

这两种语言都是不同寻常的语言，就像其他语言一样，各有利弊。作为一个写 Python 比写 Ruby 多得多，现在正在广泛学习 Ruby 的人，我可以说我开始喜欢 Ruby 超过 Python 了。语法、灵活性和功能都超出了我的想象，它已经变成了一种奇妙的语言，现在开始明白为什么人们如此喜爱 Ruby 了。

# 结论

Ruby 会杀 Python 吗？我认为不会。这总是取决于最终的用例以及开发者。有些人因为个人偏好总是更喜欢 X 而不是 Y，但我认为 Ruby 将继续与 Python 一起发展，但 Ruby 将更多地被用于使用 Rails 而不是 Django/Flask 的 web 开发，我们无法预测未来，但它们都已经是令人难以置信的语言，并将继续发展。

你怎么想呢?

编辑:更改了标题