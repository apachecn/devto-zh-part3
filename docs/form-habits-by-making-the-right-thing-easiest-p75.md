# 通过让正确的事情变得最容易来养成习惯

> 原文：<https://dev.to/easyaspython/form-habits-by-making-the-right-thing-easiest-p75>

我是一个习惯动物。我倾向于按照我喜欢的方式配置事物，锁定它们，并永远沉浸在我为自己打造的小生态系统中。当一些事情发生并开始引起摩擦时，我有效适应的唯一希望是找到一个我可以快速切换到的新配置。

一段时间以来，我使用一个您可能已经熟悉的命令进行 git 提交:

```
$ git commit --all --message 'Make the code less wonky' 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但我有这种不安的暗流在最后一段时间。`git`允许您通过以某种方式格式化您的消息来指定更长的描述。当您将提交推送到 GitHub 或 GitLab 等工具并打开一个 pull 请求时，它们将使用简短的描述作为标题，较长的描述将被插入 pull 请求的描述字段。要做到这一点，需要在提交消息中有策略地添加一些换行符，但是我经常发现自己不小心在消息的第一行结束了引号，或者完全忘记了提供长格式的描述。这就是摩擦。

```
$ git commit --all --message 'Make the code less wonky

* Fix a bug
* Add some comments
* Remove bitcoin mining malware' 
```

Enter fullscreen mode Exit fullscreen mode

GitHub 最近还增加了一个功能，让[代表一个组织](https://help.github.com/en/articles/creating-a-commit-on-behalf-of-an-organization)提交——如果你把开源作为你公司日常工作的一部分，这个功能会很有用。这个特性的一部分包括向提交添加更多的信息，需要记住更多的换行符:

```
$ git commit --all --message 'Add a bit of wonk back in

* We want those bitcoins so add the malware back
* That "bug" was a "feature" it turns out

on-behalf-of: @some-org <my.email@some.org>' 
```

Enter fullscreen mode Exit fullscreen mode

这变得相当难以控制，尤其是如果你试图[尽早承诺并且经常](https://sethrobertson.github.io/GitBestPractices/#commit)(这是你应该做的事情！).我真的想开始做更多的描述性提交，并希望在开源中有适当的属性，但这是一个很大的摩擦。幸运的是，`git`提供了一个让我适应的解决方案。

您可以配置`git`为您的提交使用一个模板，它可以存储在一个文件中。当您没有在命令行提供一个`--message`参数时，`git`将打开一个文本编辑器，您可以在其中编辑提交消息，用您的模板内容预先填充。我认为这可能是简化我的工作流程的一个很好的方式，并提示我在提交中添加更多的描述性内容。这种自动提示很简单，但是增加了一点生产力*和*改善了我与他人交流的信息，所以这是一个巨大的胜利。

我首先创建了一个符合我所期望的提交格式的模板:

```
Summary of change

Longer description of change

on-behalf-of: @some-org <my.name@some.org> 
```

Enter fullscreen mode Exit fullscreen mode

然后我告诉`git`将它用于我感兴趣的库:

```
$ git config commit.template /path/to/commit.template 
```

Enter fullscreen mode Exit fullscreen mode

设置方面大概就是这样！但是我能够立即开始收获它的好处；没有了`--message`标志(以及随后的消息),我调用提交过程的命令变得更短，并且考虑对说什么*的过程现在是独立的，这给了我一个机会停下来思考一些比仅仅说 [haaaaaands](https://xkcd.com/1296/) 更有价值的事情。*

万一我感到懒惰或健忘，模板也支持我，并提醒我添加上下文是件好事。它也总是有`on-behalf-of`提交预告片，所以我几乎不需要考虑它。总的来说，这为我节省了大量的累积时间，并让我对我在那里分享和发布的内容更加深思熟虑。即使在我没有这样配置的存储库上(因为配置有不同的需求，我还没有开始做)，我至少已经切换到总是使用文本编辑器来编辑我的提交消息。

所以这个故事的寓意不是关于`git`。让“正确”的事情成为最容易的选择是我真正养成习惯的方式。如果你和我一样，如果两件事同样简单，我会在上面翻来覆去，直到把自己逼入绝境，如果“错误”的事最简单，我就完了。因此，走出去，通过让工具为您工作，从您的工具中挤出一些生产力！

* * *

你的最佳工具配置或生产力优势是什么？