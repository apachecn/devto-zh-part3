# 界面和团队合作

> 原文：<https://dev.to/conw_y/interfaces-and-teamwork-27le>

你有没有发现和你周围的人一起工作很有挑战性？不是因为他们是坏人，而是因为你们都发现很难搞清楚谁在做什么。

也许有很多重叠的工作，很多关于谁在做什么的模糊不清，很多人无意中踩了对方的脚。你不清楚自己在做什么，别人在做什么。例如，你试图建造一些东西，但结果是，其他人也在试图建造那个东西。所以你加倍努力。然后试图合并你的代码会变得很奇怪。你发现你自己和你的同事陷入了各种各样的争论，关于我们正在建造的这个东西应该是什么以及它应该如何工作。

我最近发现了一种技术，我在最近的一个项目中使用了这种技术，它看起来非常有效。

基本上，它使用**接口**来组织团队合作。

作为开发人员，我们大多数人可能都熟悉接口的概念。它通常是位于一个代码单元和另一个代码单元之间的某种结构。所以我有了这个想法，用一个界面来让我更容易和我的一个队友一起工作。

所以我们有一个想要监控的数据库。每当某个特定的变化发生时，我们想要经历一系列的步骤，在这些步骤的最后，我们想要为一些不同的消费者产生一些输出。

我的工作是构建整个管道，但要与负责数据库的团队合作，这样他们才能确保工作按照他们的标准干净利落地完成。

[![Diagram showing team with database and me with my workflow/transformation pipeline](img/4c9b7be02de9bedbe14a6e3da26611e3.png)](https://i.imgur.com/qiZhUFS.png)

因此，从这个简短的描述中，你已经可以看出这里出现了一些棘手和模糊之处。在这里，我负责从数据库中获取一些数据，并对其进行处理，但还有另一个团队也负责同样的数据库。因此，我需要一种与队友合作的方式，这样我可以得到我想要的东西，但他们也可以做他们想做的事情，尽量不要大惊小怪。

我解决了这个问题-你猜怎么着？——一个**接口**！

所以基本上我创造了一个界面。该接口有一个方法，只要涉及到我的工作流的数据库有更新，就可以调用这个方法。(名字忘了，叫什么来着`processData`。)

[![Diagram showing team with database, me with my workflow/transformation pipeline, and the new interface between us, making life easier for both sides!](img/4c9b7be02de9bedbe14a6e3da26611e3.png)](https://i.imgur.com/8Qt5LmP.png)

这立即创造了一个很好的清晰的职责划分。

1.  数据库团队只需负责在数据库的相关部分更新时调用我的方法。只要他们在适当的时候调用我的方法，他们的工作就完成了。

2.  我所要负责的就是确保我的工作流在这个方法被调用时启动。这是在方法实现内部编写代码的问题，以生成适当的数据结构，实例化适当的类并调用适当的方法。

这是一次完美的责任分工。数据库团队可以专注于他们所熟知的东西，也就是数据库。我可以专注于我想做的事情，即建立一个工作流程并将输入转化为输出。

附带的好处是，为我的工作流编写单元测试非常容易。我不必测试或模拟整个数据库，我所要做的就是模拟我的界面的输入，并专注于实现我正在构建的实际东西，即工作流。

此外，向管理层准确反馈我的进展也更容易了。我可以告诉他们我在工作流程中取得的进展，而不是因为等待数据库团队完成他们的工作而受阻。同样，数据库团队也很清楚他们的工作是什么，他们可以稳定地交付工作，并向管理人员提供进度更新。因此，每个人都以这种方式更快乐、更有效率地工作。

在那次经历和其他一些经历之后，我可以肯定地推荐接口作为组织团队工作的一种方式，它可能会在你的项目中给你带来潜在的好处！