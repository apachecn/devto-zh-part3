# Todo-MVP:或者“为什么你不应该使用 Web 框架”——报复

> 原文：<https://dev.to/gypsydave5/todo-mvp-or-why-you-shouldnt-use-a-web-framework---the-revenge-261l>

今年早些时候，我写道:

[![gypsydave5 image](img/ae3081c50d4c953e1125e67f5f086a97.png)](/gypsydave5) [## 为什么不应该使用 Web 框架

### 大卫·威克斯 7 月 26 日 184 分钟阅读

#webdev #framework #beginners #react](/gypsydave5/why-you-shouldnt-use-a-web-framework-3g24)

这引起了一些混乱。我认为描述这种反应的礼貌方式应该是“混合，但充满激情”。

因此，因为点燃蜡烛总比诅咒黑暗好，所以我想介绍一个我正在整理的名为 [Todo-MVP](https://github.com/gypsydave5/todo-mvp) 的小项目，作为展示我已经 ~~ranted~~ 谈到的一些想法的一种方式。

但首先，我将回顾一下最初的帖子，并尝试回应一些更常见的批评。 <sup id="fnref1">[1](#fn1)</sup>

## 礼貌重述

我认为 web 框架给项目增加了额外的复杂性，从使用它们的开发人员手中夺走了控制权，并把控制权交给了编写框架的人。因此，我们被迫采用特定的方式来设计和配置应用程序，这可能不符合我们的需求或目的。在项目开始时，我们可能不会意识到这一点，但当我们意识到这一点时，我们将如此“嵌入”在框架的系统中，以至于很难做出必要的改变。

更强烈的是，对框架的依赖变成了恶性循环；当我们缺乏从简单的库构建 web 应用程序的技能时，我们将转向一个框架来构建我们的网站，当这在某种程度上不可避免地使我们失败时，我们将寻求另一个同样不合适的框架。 <sup id="fnref2">[2](#fn2)</sup>

一个特别突出的例子是使用前端 JavaScript 框架来呈现普通的 HTML 内容，这些内容可以很容易地托管在静态站点服务器上。

最后,“框架优先”的心态甚至让人们不敢尝试在没有框架的情况下编写一个应用程序——他们会自然而然地认为这一定很难，如果不困难，那么一开始就不会有框架。这是不真实的。理解和使用一组写得很好的库来构建你的应用程序就像使用一个框架一样容易，而且你不会让自己暴露于上面列出的问题。<sup id="fnref3">T33</sup>

## 回应批评

有些人似乎认为我想用 c 或汇编语言写所有的东西。没有；虽然这可能很有趣，但从时间的角度来看可能是低效的。我的问题是关于框架的。框架的一个好定义可能是:

> 你称之为图书馆的代码。一个框架调用你的代码。

如果您想了解更多细节，请阅读[这个堆栈溢出问题](https://stackoverflow.com/questions/148747/what-is-the-difference-between-a-framework-and-a-library)。但是，如果你没有猜到，在我看来，图书馆(大部分)是好人。所以——使用库(希望有好的抽象),避免框架。

其他人声称，如果他们不使用框架，他们“最终会自己构建一个框架”。这是不太可能的，除非他们想优化生产多个具有相同结构的应用程序。你不会‘建立一个框架’。

您将构建的是您最初尝试构建的应用程序，仅此而已。如果你做得对，它将足够灵活，可以在未来改变和成长。

Adrian Holovaty 在这次演讲中比我说得更好——他流利、有礼貌，并且弹得更好:

[https://www.youtube.com/embed/VvOsegaN9Wk](https://www.youtube.com/embed/VvOsegaN9Wk)

## Todo-MVP

所以为了让我的钱花在我多嘴的地方，我写了这个:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[gypsydave 5](https://github.com/gypsydave5)/[todo-MVP](https://github.com/gypsydave5/todo-mvp)

### 待办事项列表应用程序的非 SPA 版本

<article class="markdown-body entry-content container-lg" itemprop="text">

# 全 MVP

这个项目的目标是证明使用 HTML、CSS 和少量用自己选择的语言编写的服务器端代码来构建 web 应用程序是相对简单的。

它是 Todo [*最小可行产品*](https://en.wikipedia.org/wiki/Minimum_viable_product)——你能编写的最简单和最具扩展性的应用——但也许它也是你的 web 开发工具包中最有价值的玩家[。我喜欢这么想！](https://en.wikipedia.org/wiki/Most_valuable_player)

## 元待办事项

*   工作 Todo-MVP 应用程序
*   漂亮的屁股
*   好 a11y
*   简单验收测试
*   a11y 级最佳
*   以多种语言实现
*   多个 CSS 文件
*   自动化部署
*   自动化验收测试
*   ？？？
*   利润！

## Todo 应用程序

该项目包括(或将包括)以下内容:

*   许多用多种语言编写的 Todo 应用程序，每一个都服务于相同的 HTML 并实现相同的 API。
*   一个验收测试，以确认应用程序做了上述工作

## 原则

鉴于我尊重技巧和努力…

</article>

[View on GitHub](https://github.com/gypsydave5/todo-mvp)

这是我对 TodoMVC 的想法，但我没有使用各种前端框架来构建 Todo 应用程序，而是尝试用各种用不同语言编写的 web 服务器来做这件事。

它们都使用 HTTP 网络调用实现相同的 API 来控制应用程序。并且它们都呈现相同的 HTML 元素，可以用相同的 CSS 进行样式化。

我在这里试图证明，你不需要框架(你几乎不需要任何库)来构建一些工作的、可用的、你可以理解并反复构建的东西。

这个想法并不是要构建一个完整的应用程序——在现实生活中，您可能希望提供一些持久性(如数据库)、单个用户会话、身份、授权...我很乐意跳过这个例子中的所有内容，但是根据您的需要实现这些内容并不困难。 <sup id="fnref5">[5](#fn5)</sup>

其中一个实现的运行实例可以在[todo-mvp.com](http://todo-mvp.com)找到。

### Todo-MVP 需要你

[![todo list with 'contribute to Todo-MVP on it](img/5320b7da4c947e68d99499b041505ae1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lq8mH0xn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5eej57cb9k995jxrsqk.png)

是的*你！*

请帮帮我！你能花点时间用你选择的语言构建一个简单的实现吗？即使别人已经用你喜欢的语言完成了，你也可以再做一次，但是要展示不同的库，不同的方法——如果你想展示如何工作，甚至可以使用一个框架。

阅读投稿指南，编写实现，运行验收测试，并获得一个拉请求。

还有——一切都可以在各个方面得到改善！如果你能在这方面大放异彩，请随意编写一些令人惊叹的 CSS，通过发现和修复可访问性问题来帮助我改进应用程序，帮助编写一些文档...

有这么多...去做😉

* * *

1.  你真的应该看看评论和批评的原文——其中一些是喜剧黄金(以一种好的方式！). [↩](#fnref1)

2.  这也导致了关于架构模式的群体思维(框架思维)。您的应用程序可能不太适合 MVC RESTful CRUD 应用程序，但这很可能是您必须用框架构建的。 [↩](#fnref2)

3.  框架的真正意义可能是将项目结构和架构强加给你。如果你想要这个-那好，去吧。但我不认为这是一个好的价值主张。 [↩](#fnref3)

4.  但是从你的收入来看，这可能是惊人的高效——继续给孩子们开账单吧！ [↩](#fnref4)

5.  或者使用外部服务——IDaaS 越来越受欢迎。 [↩](#fnref5)