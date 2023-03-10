# 你好，虾虎鱼世界

> 原文：<https://dev.to/renegadecoder94/hello-world-in-goby-3m3e>

在本文中，我们用 Goby 处理 Hello World，这是一种用 Go 编写的类似 Ruby 的语言。要获得本文的最新版本，请查看官方文档。

## 虾虎鱼背景

[据 GitHub 项目](https://github.com/goby-lang/goby)介绍，Goby 原名 Rooby，是一种用 Go 编写的类 Ruby 语言。该语言的目标是为构建微服务和 API 服务器提供一个小环境。

除此之外，该项目在用例或样本方面没有太多可提供的。也就是说，[官方网站](https://goby-lang.org/)确实给出了一些语言特性的例子，包括:

*   并发支持
*   内置多线程服务器
*   插件系统

对于任何想尝试这种语言的人来说，可以用下面的命令在 REPL 模式下安装和运行 Goby:

```
brew tap goby-lang/gobybrew install gobygoby -i 
```

或者，您可以尝试使用 repo:
中包含的[sample runner 脚本](https://github.com/TheRenegadeCoder/sample-programs/blob/master/samplerunner.sh)运行所有示例代码片段

```
./samplerunner.sh run -l goby 
```

在接下来的部分中，我们将介绍这个解决方案以及如何运行它。

## 如何实施解决方案

正如在这个集合中多次看到的，Goby 中的 Hello World 其实真的很简单。总之，这是一行看起来很像 Ruby 的代码:

```
puts("Hello, World!") 
```

或者，我们可以省去括号:

```
puts "Hello, World!" 
```

无论是哪种情况，`puts`都会写下“你好，世界！”标准输出中的字符串。就是这样！

## 如何运行方案

为了运行这个解决方案，我们可以利用包含在[示例程序 repo](https://github.com/TheRenegadeCoder/sample-programs/blob/master/samplerunner.sh) 中的 samplerunner 脚本。如果我们的系统设置正确，下面的命令应该可以完成任务:

```
./samplerunner.sh run -s hello-world.gb 
```

或者，我们可能想要得到一个 Goby 解释器的副本。[根据文档](https://github.com/goby-lang/goby/)，有几种方法可以做到这一点，但我们现在不会深究。

与这个集合中的许多其他语言不同，Goby 目前没有在线解释器。

## 每种语言的示例程序

我已经很久没有发表这样的文章了。事实上，随着示例程序子域的建立和运行，我不打算再这样做了。然而，我最终决定，如果我能同时支持那个网站和这个网站，那是最好的。因此，我开始在这里交叉发布新的示例程序文章。

老实说，我对这种变化很兴奋。我可以继续在其他网站上写有趣的技术内容，同时在这里添加个人风格。此外，当我在这里发帖时，新内容会发送给我的所有订户，因此我扩大了自己的影响范围。不这样做就太傻了！

和往常一样，如果你喜欢这个系列或者你只是想表达你的支持，请到我的[会员页面](https://therenegadecoder.com/members/)订阅。