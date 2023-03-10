# 帮我删了你的推文！

> 原文：<https://dev.to/petarov/help-me-delete-your-tweets-3a6>

好吧，我为 clickbaity 的标题道歉，为了弥补它，我简单地说一下。

**TL；博士**，我正在寻找黑客来帮助我改善一个 Python 脚本。

几年前，我想删除我所有的推文和赞，但仍然出于私人目的保留它们，主要是为了历史参考和个人存档。因为我在 Twitter 上并不活跃，删除我的账户可能更有意义。但是嘿！我还是想时不时地发表一下自己无知的观点。

长话短说，我黑了这个消耗 Twitter API 的小 Python 命令行工具。它会找到指定日期时间点之后的所有推文和/或赞，将它们放在 ePub book 中，然后(可选地)从 Twitter 中删除它们。因为它只是一个命令行工具，所以在 cron 作业中运行它也很简单。到目前为止，一切顺利，看起来还不错，但是有一些限制。我不知道如何用 [Tweepy](http://www.tweepy.org/) 保存 Tweepy 图片，ePub 电子书可以使用更多选项，如封面和封底图片、目录、布局选项等。

所以我想这是一个呼吁捐款。如果您正在寻找一些开源项目，并且您觉得这很有趣，我们非常欢迎您的加入。我实际上不是 Python 开发者，而是 Python 黑客，所以这更像是*为了好玩而黑它，但是仍然让它有用*的事情

这是 GitHub repo，感谢您的阅读！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [佩塔罗夫](https://github.com/petarov) / [闭嘴鸟](https://github.com/petarov/shut-up-bird)

### 🐦像老板一样存档和删除你的推文和赞

<article class="markdown-body entry-content container-lg" itemprop="text">

# 闭嘴小鸟

存档并删除你的 Twitter 帖子。

你想扔掉你的旧推文或*赞*，但仍然把它们很好地组织在某个地方？或者你想设置一个 cron 来定期清理你的时间线？

这个工具从一系列 Twitter 帖子或赞中创建一个 [ePub](https://en.wikipedia.org/wiki/EPUB) 电子书，然后(可选地)从你的时间线中删除它们。

# 装置

需要 Python `2.7`或者`3.x`。

## 包装

要在来自 [AUR](https://aur.archlinux.org/packages/shut-up-bird) 的 **ArchLinux** 上安装，请运行:

```
yaourt -S shut-up-bird 
```

## 指南

运行`make`或`pip install -r requirements.txt`。

# 设置

创建一个新的 Twitter 应用程序。名字不重要。

打开新 Twitter 应用的`Permissions`页面，确保选择了`Read, Write and Access direct messages`，否则该工具将无法删除任何推文。

不带任何参数运行以初始化工具:

```
$ python2 shut-up-bird.py
Please provide your Twitter app access keys 
```

进入…

</article>

[View on GitHub](https://github.com/petarov/shut-up-bird)