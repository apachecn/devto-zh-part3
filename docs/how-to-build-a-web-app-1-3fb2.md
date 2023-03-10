# 如何构建 Web 应用程序

> 原文：<https://dev.to/gtanyware/how-to-build-a-web-app-1-3fb2>

[设置> > >](https://dev.to/gtanyware/how-to-build-a-web-app-part-2-5cln) | [故事> > >](https://dev.to/gtanyware/how-to-build-a-web-app-part-3-5ae3) | [文字，文字，文字> > >](https://dev.to/gtanyware/how-to-build-a-web-app-part-4-1a52)

# 简介

这是关于 web 应用主题的系列文章的第一篇。

首先，什么是 Web App？我将把它定义为在浏览器中运行并控制自身事务的任何程序。然而，这并不是说 web 应用程序可以在浏览器中做所有的事情；许多人会调用服务器来提供诸如数据库访问之类的服务，最有可能的是使用 [REST](https://dev.to/search?q=rest) 协议。在本系列中，为了尽可能简单，我将使用一个完全基于浏览器的例子。

### 我的目标受众

我写文章的对象主要是那些技术水平从初级到中级的人。专业人士会发现很多不同意的地方，但他们有技能和能力把事情做得更好，而我们其他人，努力在即将到来的技术浪潮中保持漂浮，寻找简单的方法做事。这些变得越来越难找到，我的使命是尽我所能提供帮助，部分是通过从过去吸取一些在匆忙接受新的事物中被遗忘的教训。

警告:我将在本系列中使用的代码和示例也是针对同样的人，不太可能受到在大型团队中工作的专业人员的青睐。然而，大多数网站都是相对较小的项目，我不相信要用大锤去敲碎坚果。无论如何，那些专业人士不需要像这样的一系列文章，因为他们已经知道的比我多得多。

## 为什么要做 Web App？

构建 web 应用程序有几个原因:

1.  你已经签约做这件事了
2.  你需要在简历上写点东西，证明你是一个真正的程序员
3.  你想测试新的想法
4.  你这么做是为了好玩

如果你是出于第一个原因，那么你很可能会在如何处理工作上受到很大的限制。单从招聘启事来看，大多数潜在雇主对他们希望软件如何构建有一个固定的想法。大多数人要求一份令人沮丧的相似的技术清单，代表当前公认的关于如何做事的智慧，并且对鼓励新想法不抱什么希望。

这不可避免地导致了原因 2。各地的程序员都在用当前最流行的技术来填鸭式地训练自己，并祈祷他们能在更聪明、更新的东西出现并使他们成为多余之前找到工作。很难适应快速的进化。

最后两个原因只是简单的自我放纵。你想要开心，为什么不呢？你只有一次生命。

## 框架和工具

Quora 上最近的一个问题是，当前大量的框架和工具是否对编程有负面影响。有两种反应给出了截然相反但同样有效的答案。首先，没有这些工具就不可能有效竞争，因为用传统的编程方法构建应用程序需要更长的时间和更多的成本。第二，使用 Lego 方法会导致一个庞大的依赖链，最终会将整个宇宙拉进来，导致代码膨胀，难以维护。

第一个答案是正确的，因为如果你不能竞争，你就没有生意。它假定唯一重要的考虑因素是建造某物的前期成本，而不可量化但次要的维护可以安全地忽略。遗憾的是，这是主流观点。但是如果你关心维护，这些工具的效果确实是负面的。我之前写了一篇文章，引用了我称之为*蒂奇马什效应*的东西，来说明建造一个不能被所有者维护的东西是多么容易，因此它的使用寿命很短。就像生活中的大多数事情一样，如果你能找到一个好的妥协，那总是最好的。

## 本系列中的零件

这些是我将在本系列的后续部分中讨论的主题:

1.  正在设置。我假设你想给别人看你的作品；这意味着你要有一个网站，让人们可以指向它。许多想成为程序员的人似乎不太重视这一点，但我认为我们都应该进行一点自我宣传。
2.  故事。在开始编码之前，一定要记录要构建的用户级视图，否则就没有测试结果的依据。在这里，我将打破常规，描述一种保持故事可见的编码方法，以便它们可以被编写它们的领域专家理解。
3.  库组件。大多数系统包括可以被视为独立实体的部分，这些实体在其他地方可能有用。不要在你的项目中直接构造这些；将它们构建为单独的库。我的文章将包括一个例子。

## 即将到来...

本系列的下一部分，[设置系统](https://dev.to/gtanyware/how-to-build-a-web-app-part-2-5cln)，包括设置一个面向公众的 WordPress 系统，包括如何定制一个主题，给你一个可以放置代码的干净页面。

标题图片由[克莱门特 H](https://unsplash.com/photos/95YRwf6CNw8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄