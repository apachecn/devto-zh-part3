# 伪代码如何写伪代码

> 原文：<https://dev.to/flippedcoding/how-to-write-pseudo-code-2jfe>

你遇到过非常复杂的编程问题吗？一个问题是，你可能可以写出逻辑，但你不太确定应该使用的语法？编写伪代码是一个很好的起点。

## 这是什么

伪代码是一种“语言”,您可以编写所有的编码逻辑，而无需编写一行特定于语言的代码。你在算法研究中经常看到这种情况，尤其是机器学习算法。这并不意味着您不能将其用于 web 开发。

## 你为什么会使用它

有些项目非常庞大，如果你不花时间写一点伪代码，你可能会迷失在实现代码的海洋中。当你写一些伪代码的时候，它给你一个机会去真正地思考潜在的问题。您可以查看纯逻辑和程序流，而不必担心代码如何运行。

在开始键入真正的代码之前编写伪代码也将有助于您更快地完成项目。把它当成一个蓝图。你知道每样东西需要放在哪里，以及每样东西是如何协同工作的。因此，当你进入实际的构建阶段时，你没有那么多要考虑的，因为你已经考虑清楚了你需要做什么。

最棒的是，伪代码不依赖于任何编程语言。你刚刚写出来的逻辑可以被任何人理解并翻译成他们选择的语言。它让您可以自由地重用和改进您正在构建的应用程序的架构。

伪代码更微妙的用途之一是与他人分享。有时您会有一个特定的逻辑可以跨多个项目使用，但是它们都使用不同的编程语言。当你有了可用的伪代码，你可以把它给其他程序员，他们可以用他们需要的任何语言来写这个逻辑。

另一个很棒的特性是你可以用任何你喜欢的格式写伪代码。你可以使用学术格式。它的结构和细节令人难以置信，但它往往涉及大量的数学。或者你可以写出一个你期望你的代码做什么的简单大纲。

## 怎么写

下面是我在一篇学术论文中写的一些伪代码的例子:

[![pseudocode-ex.png](img/a74180b36d83eade3992ee62cfca376b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_5wT3KyX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1556193559488/mwN-ITK1f.png)

我将是第一个承认这对于 web 开发来说可能是多余的。如果您发现自己在使用 LaTex 编写伪代码，您可能会让它变得比您需要的更复杂。很有可能在 Word 甚至记事本上快速写一点就足够了。

下面是我为我的一个 web 开发项目编写的一些简单伪代码的例子:

```
IF userlogin = true
    API call to get user data
    Assign data to variables
    Re-route user to dashboard
ELSEIF userlogin failed more than 3 times
    Don't allow more attempts
    Send user notification email
    Re-route user to home page
ELSE
    Log bad login attempt
    Show error message
    Clear login form 
```

Enter fullscreen mode Exit fullscreen mode

你不必对你的伪代码非常精通，但是通常情况下，你能装入的细节越多，写真正的代码就越容易。把它想象成你正在为你的程序写一个大纲。这给了你一个机会去真正地思考你想要完成什么，你可以确切地看到所有的代码是如何组合在一起的。

对于伪代码，您需要关注的主要问题是:

*   程序的逻辑流程
*   程序中复杂部分的细节
*   一致的格式

除了一些硬核思维，写伪代码真的不需要太多。当你写的时候，你会开始看到可以添加更多细节的地方或者可以删除一些细节的地方。记住，这主要是给你用的，所以用对你有意义的方式写。

个人比较爱伪代码。当我开始输入真正的代码时，它帮助我保持思路清晰。当您已经计划好所有的逻辑时，您就有更多的时间来试验性能和优化。最重要的是，当你深入代码编写的时候，你不需要努力思考(为有计划的懒惰欢呼)。

你怎么想呢?你认为伪代码值得花时间吗？或者你宁愿直接跳到代码里？

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)