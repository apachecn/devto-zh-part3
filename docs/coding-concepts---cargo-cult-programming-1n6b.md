# 货物邪教编程！

> 原文：<https://dev.to/chris_bertrand/coding-concepts---cargo-cult-programming-1n6b>

我正在看/听西蒙·布朗关于 2018 年 T2 模块化巨石的精彩演讲。

在这篇文章中，他提到了一个叫做 **Cargo Cult Programming** 的术语，这真的引起了我的共鸣。

我最近加入了一家新公司，拥有新的编程语言、新工具、新流程和新团队。事实上，它几乎是所有东西的新版本。

这让我在最近几个月做了很多学习。相对来说，我比较有经验，在学习新东西的时候，我喜欢研究“为什么”而不是“T2 如何”。我意识到这是**而不是最常见的方法**。

当用户的任务是添加到现有的代码库，扩展当前的解决方案时，他们很可能会检查以前是如何完成的，为他们的解决方案复制方法。他们可能/会盲目地遵循这种模式，因为这是以前的做法。额外的积木被添加到塔顶，没有质疑这样做是否正确。如果每个人都这样做，这最终会发生。

[![Related image](img/c9d9d8db41eaa5e44a1c33d65a7bb5e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lnqG2QHH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/719a7b6d54fe98b9933e4f4ab441c412/tenor.gif%3Fitemid%3D4594310)

这就是术语' **Cargo Cult Programming** 的来源。

维基百科是这样解释的:

> 货物邪教编程是[计算机编程](https://en.wikipedia.org/wiki/Computer_programming "Computer programming")的一种风格，其特点是仪式性地包含没有实际用途的代码或程序结构。Cargo cult 编程是典型的程序员不理解他们试图解决的错误或明显的解决方案的症状(比较[猎枪调试](https://en.wikipedia.org/wiki/Shotgun_debugging "Shotgun debugging")、[深度魔法](https://en.wikipedia.org/wiki/Deep_magic "Deep magic"))。<sup id="cite_ref-1">[【1】](https://en.wikipedia.org/wiki/Cargo_cult_programming#cite_note-1)</sup>术语 cargo cult 程序员可能适用于不熟练的或新手的计算机程序员(或对手头的问题没有经验的人)[将一些程序代码](https://en.wikipedia.org/wiki/Copy_and_paste_programming "Copy and paste programming")从一个地方复制到另一个地方，而很少或根本不了解它是如何工作的，或它在新的位置是否需要。
> 
> Cargo cult 编程也可以指盲目应用[设计模式](https://en.wikipedia.org/wiki/Software_design_pattern "Software design pattern")或编码风格而不理解设计原则背后的原因。例子是给自解释代码添加不必要的注释，过分遵守特定的[编程范例](https://en.wikipedia.org/wiki/Programming_paradigm "Programming paradigm")的惯例，或者为[垃圾收集](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science) "Garbage collection (computer science)")会自动收集的对象添加删除代码。

## 普遍问题？

想象一个场景，您正在处理一个 bug，寻找导致错误的代码。你不太确定发生了什么，所以你。

1.  谷歌错误。
2.  你发现一个 StackOverflow 问题。
3.  搜索最热门的答案。
4.  将解决方案复制并粘贴到您的代码中。
5.  试着调试一下，看看这是否能解决你的问题。

是的，所以你把它登记好，然后继续前进。

听起来熟悉吗？

我们为什么要这么做？在我们的实现中，为什么我们盲目地采用这个片段并照原样使用它呢？

用例可能不一样，所以如果解决方案是一样的，我会很惊讶。除了简单的例子，理解解决方案背后的推理比解决方案本身更重要。有很多事情是你不懂的东西做不了的。你不能修改、改进或测试它。你不能记录它，也不能拥有它。

## 技术趋势、流行语和微服务

我们都喜欢新事物，管理层似乎尤其喜欢追随流行趋势，跟上技术进步。

大多数团队现在将遵循敏捷方法。TDD 和自动化测试在某些场景下非常有用，持续集成消除了基础设施团队的大部分开销，大数据和人工智能可以极大地提高用户满意度和容器化，最近**微服务**将我们的旧整体架构转变为更小的自包含服务。

这些进步中的每一项都有其自身的优点，我不会宽恕任何一项。我的困境是我们是否需要在所有的过程和代码中采用它们？我们看到来自**网飞、脸书、Twitter** 的博客帖子展示了他们的使用如何改变了他们的工作方式。如果大公司认为它们是必要的，我们不也应该这样做吗？这是货物邪教编程再次抬起它丑陋的头。

我们需要理解当前设计中的问题，为什么会出现这些问题，以及如何在未来抛弃这些问题。是的，这些新的过程可能会帮助我们解决问题，但是盲目地跟随它们，抱着微弱的希望，这不是前进的方向，也没有任何逻辑意义。

我特别提到微服务，因为许多公司似乎正在进行这种转变，列举了以下**好处**:

*   更快的开发时间
*   高可扩展性
*   易于增强
*   易于部署
*   自主团队可以自由选择技术

有了这样的名单，还有什么好考虑的？让我们都来赶时髦吧！

[![Image result for jump on the bandwagon gif](img/ae8af3136e62b1ccfeb1e1c3bc2f7792.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gNiMAg0I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://gifimage.net/wp-content/uploads/2017/09/bandwagon-gif-7.gif)

稍等...这种方法有什么缺点吗？

*   建筑复杂性

在整体架构中，复杂性和依赖关系的数量存在于代码库中，而在微服务架构中，复杂性转移到实现特定领域的各个服务的交互中

*   操作复杂性
    *   如何以可扩展且经济高效的方式调配资源
    *   如何在不增加工作量的情况下有效地操作几十个或几百个微服务组件
    *   如何应对缺乏标准和包含不同技术和具有不同技能的人员的异构环境
    *   如何处理版本控制
    *   如何跟踪和调试整个系统的交互
    *   如何跟踪数百个代码部署管道及其相互依赖性

这些摘自亚马逊自己的“[微服务挑战](https://docs.aws.amazon.com/aws-technical-content/latest/microservices-on-aws/challenges-of-microservices.html)”白皮书。我不知道你怎么想，但在我看来，缺点比好处更可怕。再说一次，我不是说这不是正确的方法，但是除非这些好处大于缺点，否则你从这种方法中能得到什么？

## “公共”问题。

很简单，停止使用 **Public** 关键字，停止自动创建 **Public** 类。我们为什么要这么做？

使用 public 关键字的问题是您错过了与封装相关的好处。为什么我们如此频繁地使用它？这几乎是我们在创建类时使用的默认词，所有示例都将使用公共类，向导和代码片段将实现公共类。是时候停下来了。有多少人拥有公共脸书帐户？这个世界上的大多数事情都是私人的，我们的班级也应该如此。默认情况下将它们设为私有，然后如果您需要将它们设为公共，那么就更改它们。

## 丰富的经验带来巨大的忧虑。

你在一个领域呆得越久，你就越不会天真地认为新的工具或过程会带来改进。今天的大部分想法来自于这个领域几十年的老研究，现在终于被接受了。一旦某样东西被大众接受，就更容易对完全接受它感到舒服。也就是说，如果这样做是对的。

> **“好的** 判断 **来自** 来自 **经验** ，而 **经验来自** 来自不好的判断”
> 
> 丽塔·梅·布朗

所以，你可以继续在网上寻找解决问题的方法和教程；不断探索新的语言、框架和实现。只是要意识到它们为什么工作，而不仅仅是 T2 如何工作。我们都从自己的经历和错误中学习，因此了解基本面将使你在未来不会继续走上同样的道路。