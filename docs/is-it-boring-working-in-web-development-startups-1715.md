# 在 Web 开发创业公司工作很无聊吗？

> 原文：<https://dev.to/ivarojha/is-it-boring-working-in-web-development-startups-1715>

## 开场白

绝大多数初创公司通过移动应用或网络应用提供软件即服务。许多初创公司都试图转向纯移动应用，但还没有真正得到消费者的认可，因为与移动应用相比，网络界面允许更多的控制和关注。除非我们发现一种新的激进的媒介，让人们离开他们的电脑，否则网络应用程序将会过着奢侈的生活。

许多有抱负的软件工程师认为 web 开发很无聊，*“那你做什么呢？改变一个按钮的大小？”:D* 。事实上，这不是一个错误的说法。然而，这是一个不完整的陈述。大多数由个人启动的项目已经完成了 80- 90%,但还没有准备好投入生产，这是因为它们在无聊的部分停止了。让我们回顾一下将想法付诸实践的一般流程。

## 构思和模型

几乎每个创业公司都在试图解决企业或普通网民面临的一个问题。真正的乐趣在于与这些用户交谈，并制造出他们都会使用或付费使用的产品。如果你自己也是产品的最终用户，那么这项工作就一点也不像是工作，因为一直以来，你都感觉自己是在为自己工作。

一旦你对你要解决的问题有了一个好的想法，手头的第一个任务就是设计一个线框模型。一般来说，你只会坐在一个专注的 UX 设计师旁边。列出所有的用户操作，以及你的应用程序将如何对此做出反应。最终，你将拥有多个屏幕，这将定义用户在你的应用程序上的整个旅程。这是你对应用程序的后端结构有一个模糊概念的时候。

## 设计与建筑

与几个其他开发人员(可能是技术领导)讨论将作为系统主干的架构，牢记未来可能的需求，即未来规格的任何变化都应该很容易纳入当前架构。有时候，开发人员试图避开这一部分，直接进入开发。当代码变得复杂时，这会在后期造成混乱，并导致产品发布中不必要的延迟。然后你还是启动了它，这导致了产品中的错误，然后你又花了几个星期来修复错误。建议您将文档中的所有内容都写下来，因为当您实际编写代码时，您可能会忘记一个或多个特性。此外，设计文档将有助于团队和任何其他想中途加入您的开发人员的参考。

估计用户、服务器和数据库的数量以及最小化服务器成本也属于这一部分。你不想为大部分时间处于闲置状态的服务器买单。根据你的应用程序，你必须决定服务器的 CPU 核心数、内存和磁盘大小、数据库类型等。有一些设计决策，比如哪个进程必须在哪个服务器上运行。不是每个进程都在一台服务器上运行。一些流程可以在另一台服务器上执行(其唯一目的是执行低优先级的流程密集型任务)，从而减少服务器上的负载并确保更快的响应时间。做出这种决定的知识是随着时间的推移而获得的。这就是为什么船上至少有一名高级工程师是很重要的。

## 后端和前端

一旦你确定了拼图游戏中每一块拼图的位置，你就开始搭建每一块拼图，最终将它们拼在一起。你戴上耳机，将设计文档转换成实际的代码，经常一路喝着咖啡，为关键组件编写单元测试。通常，你会希望后端代码完全独立于前端。人们编写 API，然后在开发前端时公开他们的端点供以后使用。与此同时，设计师将完成设计的第一个版本(转换线框到实际的用户界面设计)。

现在，您开始开发最终用户将与之交互的部分。有些人不喜欢这一部分，认为这是重复和无聊的。然而，这就是你的应用的定义。不管你的后端代码有多漂亮，你的架构有多优化，如果你没有把这部分做好，你就不能让用户加入进来。开发前端的最好方法是成为你产品的用户。这样你就可以避免做出妥协。

## 测试并发布

最后，当你组装好每一个组件后，产品就可以使用了。在发布它之前，您将经历手动测试阶段。测试一个产品的最好方法是观察两种人使用这个产品:一种人知道产品的每一点用法，另一种人不知道这个产品能提供什么。前者通常由专门的手动测试人员和/或自动化 [selenium](https://en.wikipedia.org/wiki/Selenium_(software)) 测试来完成。后者是你的同事或密友或 beta 测试者。利用他们的反馈来进一步改进 UX 和修复错误。

创业公司本质上是敏捷的。我们快速移动，打破东西。可以说，6 个月内发布一个最低限度的可用产品比 5 年内发布一个完整的产品要好。你也必须在竞争中生存下来。当您认为它足够适合您的最终用户时，您就可以发布它。有人曾经说过“在你启动它之前，休息一周，与家人和朋友共度美好时光。回来后，作为最终用户使用您的应用程序”。这是因为在工作了这么长时间和努力之后，你对这个应用程序产生了情感上的依恋，这导致了妥协。然而，这种做法很少被遵循，因为很难控制情绪和释放的冲动。人们在发布产品后会去度假，然后回来利用直接的客户反馈来塑造产品。

## 后记

如果你读到这里，你很享受这个过程，不是吗？为了简单起见，已经跳过了许多次要的细节，然而，总的来说，整个周期或多或少是相同的。此外，如果你从一个在不超过 15 名工程师的初创公司工作的产品开发人员的角度来看，这是准确的。有些工程师从事完全不同的工作，创造出完全不同的效果，用户无法直接看到。他们项目的一些例子是:压缩和延迟加载图像，人工智能回复支持票，通过学习过去的流量趋势和猜测未来的流量自动缩放服务器等等。有些项目无论如何都不会对产品产生任何影响，相反，它们帮助其他开发人员高效地工作，并使他们能够在几分钟内将代码无缝地部署到产品中，从而提高工程生产率。底线是，你选择去做那些你觉得有挑战性的项目，那些让你在晚上回家时感到最大满足的项目。

最初发布于[rookieslab.com](https://www.rookieslab.com/posts/is-it-boring-working-in-web-development-startups)