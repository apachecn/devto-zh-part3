# 不要让庞大的代码库吓到你！理解他人代码的技巧和工具

> 原文：<https://dev.to/mjraadi/don-t-let-that-huge-codebase-scare-you-tips-and-tools-to-make-sense-of-other-people-s-code-3lgb>

<figcaption>Image: Cherlat.com, Getty Images/iStockphoto</figcaption>

在每个开发人员的一生中，当他们开始一项新工作、加入一个开发团队或决定为一个开源项目做贡献时，都会有这样一个时刻，你会面临一个新的、庞大的、不熟悉的代码库和一个需要修复的 bug。代码库的规模是你从来没有真正见过的，但是不要担心，我将分享一些技巧和工具，告诉你如何在不发疯的情况下理解别人的代码。

1.  你不需要知道所有的事情🤯

    对我来说，这是一个游戏规则的改变，我意识到我不能理解所有的事情。许多初学工程师都雄心勃勃，想要阅读一切。精神好；然而，不，你不能，就这样。重要的是区分你想理解什么代码，跳过什么代码。

2.  阅读文件📖

    我开始一个新项目的第一步是阅读所有可用的文档或自述文件。这让我能够熟悉代码库的设置、功能、风格和其他重要部分。文档的某些部分可能已经过时了，但是看到代码的演变也有助于理解项目的历史。不幸的是，有时文档并不完整，而且可能是错误的。

3.  使用命令行搜索工具🔍

    当您第一次钻研包含几千行代码的代码库时，并不总是很容易立即见效。但是我在这么多行代码中找到出路的秘密武器是命令行搜索工具，比如`grep`和`ack`。搜索一个唯一的字符串或关键字是一个很好的方法，可以在不进入文本编辑器的情况下找到功能的位置。我将演示如何使用这种方便的方法来立即找出我需要查看什么文件来修复一个 bug。

    ### 一份实用的外卖

    我最近开始为开发源代码做贡献，我强烈推荐每个人也这样做。它有一个相当大的[代码库](https://github.com/thepracticaldev/dev.to)，维护者非常受欢迎。我相信这是初级开发人员为开源做出贡献并将他们的知识付诸实践的绝佳场所。
    我发现了一个错误，[报告了这个问题](https://github.com/thepracticaldev/dev.to/issues/2673)，我想尝试自己解决这个问题。考虑到 DEV 的后端是用`Ruby`开发的，我不是 Ruby 开发者，但是我需要修复的是返回给浏览器的 HTML 代码。我不知道我需要看什么文件，也不知道函数在哪里。

    ### Grep

    我打开开发工具，意识到问题中的`<div>`元素有一个类名`ltag__twitter-tweet__video`。通过`grep`运行字符串，我找到了我需要查看的三个文件:

```
$ grep -iRl "ltag__twitter-tweet__video" app/
app/assets/stylesheets/ltags/TweetTag.scss
app/views/liquids/_tweet.html.erb
app/liquid_tags/tweet_tag.rb 
```

Enter fullscreen mode Exit fullscreen mode

很酷，对吧？使用`grep`的好处是它可以在你可能使用的任何 nix 发行版上使用。它在许多不同的环境中非常有用。要了解更多关于 grep 的信息，请务必查看[这篇文章](https://linuxhandbook.com/grep-command-examples)。

### 确认

[Ack](https://beyondgrep.com) 是“一个类似 grep 的工具，针对程序员进行了优化。”默认情况下，它会递归搜索(即您的项目)，而忽略 VCS 目录，如。git，它有一些方便的工具，可以帮助您用更少的击键次数来探索代码。
以同一个 grep 为例，下面是我们如何搜索“ltag _*Twitter-tweet*_ video”:

```
$ ack "ltag__twitter-tweet__video" app/
app/assets/stylesheets/ltags/TweetTag.scss
44:  .ltag__twitter-tweet__video{

app/views/liquids/_tweet.html.erb
9:        <div class='ltag__twitter-tweet__video'>

app/liquid_tags/tweet_tag.rb
30:          el.getElementsByClassName("ltag__twitter-tweet__video")[0]   .style.display = "block"; 
```

Enter fullscreen mode Exit fullscreen mode

Ack 是我最信赖的搜索工具，我认为你将会从它作为 grep 替代品的使用中获得很多价值。我强烈建议学习如何使用 ack。考虑阅读此以了解更多信息。

1.  问！🙋

    最后，如果你还是卡住了…那就问吧！在开始时，获得项目知识的一个好方法是与团队中更熟悉代码库的开发人员配对。这个人可以提供与项目相关的特定设计模式、测试、过程和第三方代码的高层次见解。他们也可以给你更多关于项目的历史背景，以及为什么最初做出某些选择。

这些是我的建议！希望这有助于完成不熟悉代码的艰巨任务。请尽量不要紧张。呼吸，记住这些就没事了！你有哪些？我也很想听听你的建议和窍门。

### 关于我

我是一名全栈 web 开发人员，也是一个由才华横溢的专业自由开发人员组成的小组 Bits n Bytes Dev Team 的联合创始人，我们提供基于尖端技术的定制 web 应用程序开发服务，以满足客户独特的业务需求。

我可以被雇佣，你可以通过[https://www.bitsnbytes.ir/portfolio](https://www.bitsnbytes.ir/portfolio)查看我的投资组合网站，或者通过 [raadi@bitsnbytes.ir](mailto:raadi@bitsnbytes.ir) 联系我。