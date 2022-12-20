# 学习灵丹妙药如何让我成为更好的程序员🥃

> 原文：<https://dev.to/aleccool213/how-learning-elixir-made-me-a-better-programmer--57jg>

> 想要更多这样的精彩内容？注册订阅我的时事通讯，请访问:[Alec . coffee/注册](//alec.coffee/newsletter)

在熟悉了一些编程技术之后，开发人员通常会止步于此；你的工作和你维护的系统可能都使用一种或两种语言。你开始一次又一次地使用相似的模式来解决相同的问题。Elixir 是一种相对较新的编程语言，它让我看到了打破这种停滞思维的新技术。学习一门新的编程语言可以向你介绍使用现有技术永远不会遇到的技术。在设计新系统时，它扩展了您的工具箱。想象一下，一个木匠被某一套工具束缚了好几年，他们所能建造的东西会受到限制。在学习编程语言多年后(学校、合同工作、合作等)，摆脱专注于尽可能快地完成工作的思维模式令人耳目一新。没有时间表告诉你以什么速度学习，也没有同事依赖你来完成你正在做的事情。我发现在这种放松的环境下，更容易消化更大的认知负荷。

E.g. of pattern matching. This and many other features of the language make it expressive and easy to read.

| ![pattern-matching-example](img/e4ac7234c1140cf7b8d72b5f93e2207e.png) |

### 你体内的快速反应

*   Elixir 只是 Erlang 之上的语法，Erlang 是构建在 BEAM VM 之上的久经考验的语言

*   语法类似于 Ruby，所以学习语法很容易也很快，特别是对于熟悉它的开发人员来说

*   我提到它的功能了吗！(纯粹的函数式编程 IMO 在认知上是值得投资的，[点击此链接](https://medium.com/making-internets/functional-programming-elixir-pt-1-the-basics-bd3ce8d68f1b)了解 Elixir 如何利用它)

学习一门新开发的编程语言的好处之一是它建立在现有的最佳实践之上。当创建者花时间思考其他开发人员经常面临的问题时，就会出现这种情况。“状态管理很难”，“零时间部署新代码很难”，“维护我的系统很难”，这是每个开发人员都会想到的。Elixir 希望让这些问题不那么棘手，并使用封装在 VM 周围的函数式方法来实现这一点，VM 将分布式/并发编程视为一等公民。例如，开发人员发现了 Ruby 语法的高效性、函数式编程的可维护性和 Erlang 的可伸缩性。这种语言的这些特性使它成为一个引人注目的展示，展示了最近构建的语言的能力，正如上面的模式匹配例子所展示的那样。

> 例如，开发人员发现了 Ruby 语法的高效性、函数式编程的可维护性和 Erlang 的可伸缩性。

### 电线连接到电线

OTP in the anime-flesh

| ![otp-wires](img/2f6982b771236d9719534f3becb5046f.png) |

Elixir 坚如磐石的基础建立在名为 [OTP](https://en.wikipedia.org/wiki/Open_Telecom_Platform) 的库之上。OTP 是处理分布式编程中出现的所有问题的一种优雅方式，比如跨节点工作、处理异步消息等。它不仅是一个函数库，也是一个工作范例。这保持了系统和大型团队之间的一致性。不是单个进程处理你的整个应用程序(想想 Node.js)，而是许多孤立的进程组成一个 Elixir 应用程序。这些进程使用消息相互通信。这释放了许多很酷的特性，流程现在可以跨机器运行，因为消息只能是不可变的，不允许有指针。

你内心的批评家会说，使用这种新语言的潜在缺点是它没有经过实战检验。通常这是一个合理的批评，但对于灵丹妙药却不是这样。它所基于的虚拟机药剂已经很老了。Erlang 最初的开源版本是在 1998 年，在此之前，爱立信已经在内部使用了很长时间。由电信网络使用，这些是不允许停机的关键服务。例如，这就是非常酷的[热代码发布](https://github.com/edeliver/edeliver)特性的由来，它使开发人员能够在不关闭服务器的情况下发布新的 Erlang/Elixir 代码。

### 我的经历

A candid photo of me reading Elixir in Action

| ![pattern-matching-example](img/6dd1eda921d974b62a4dfcb56dd311ce.png) |

去年，一位同事邀请我加入他的读书俱乐部。"让我们学习这门新语言。"我听说这是新的热点，所以我说，“当然！”。我们每个月都会花几个小时来复习书中的一章，即[灵药在起作用](https://www.amazon.ca/gp/product/161729201X/ref=as_li_tl?ie=UTF8&camp=15121&creative=330641&creativeASIN=161729201X&linkCode=as2&tag=coffeedrive09-20&linkId=97d40dff77b7869475d6ee283c6501d2)。起初，加入是令人生畏的，因为与小组中的其他成员相比，我的资历要浅得多，但我尝试了一下。接下来是许多精彩的讨论和对我以前从未深入过的话题的深刻见解。我很感激以前的自己同意加入，因为我不仅学到了很多东西，还与公司的同事建立了联系，否则我将永远不会与他们建立联系。它帮助我通过 Flipp 采用事件驱动系统(想想 Kafka ),让我接触到管理进程间状态的良好实践。保持过程的小、纯和功能性是良好的工程实践，也是灵丹妙药的支柱。我不需要立即构建任何东西或完成一项任务，我为学习的快乐而学习，并从中获得了很多。

### 常见的评论和问题

| ![pattern-matching-example](img/1208e1443ca7ba3143e1f4505338e886.png) |

> 我的团队在过去一周学习了 3 个 Javascript 框架后，不得不学习这个，这让他们很不高兴。

一旦您开始构建必须扩展或需要处理数百万个请求的东西，您的待命票证就会增加。其原因通常是你无法预测这种规模的流量，新功能的推送通知会出现，每个人都开始攻击你的 API。你目前是如何处理的，比如 Node 或者 Ruby？你只需增加你的箱数，然后在装货完毕后减少它们。这变得很昂贵，开发者不应该只是为了解决问题而砸钱。Erlang VM 进程(不同于传统进程)是固定大小的，这就是 **mega** 。在某种程度上，这基本上解决了这个问题。知道记忆过程有多少，给你神一样的能力。VM 可以精确地告诉服务器它可能会使用多少内存。例如，您可以用 HTTP 状态代码 429 来响应客户机，而不是摔倒并重启机器。不会再在凌晨 1 点意外加载内存而吵醒开发人员了！

> 好吧，这太棒了，错误是怎么处理的？

错误是长生不老药中的一等公民。进程很小并且是隔离的，所以当抛出一个错误时，整个应用程序进程不必转储它的堆栈，只需转储隔离的进程。当错误发生时，它们更容易调试，因为过程代码很小(按照 Elixir 约定)。进程非常小，以至于每个进程都有一个监视器(OTP 的另一个好处)，当一个进程死亡时，它可以运行一些代码。例如，一个示例监视器可以重新启动进程，以便它可以接受更多的消息。

Everyone gets a monitor

| ![pattern-matching-example](img/304f0f5bbc9415b0dbdeb9cc64ad28bb.png) |

此外，在 Javascipt 中有一个模式匹配的提议，这非常好。明显的证据表明每个人都在喝...(要说出令人吃惊或高兴的事情)听着...*仙丹*。

🚒

| ![pattern-matching-example](img/1a66508355a8aa4bad52eab2acda3c5f.png) |

#### 前进的道路

我希望这篇介绍向你展示了长生不老药的一些威力，并鼓励你学习更多。我只是抓住了光束虚拟机的可能服务。我给大家展示了这张图表，它显示了与其他流行语言相比，Elixir 在 Stackoverflow 上的受欢迎程度:

Perspective

| ![pattern-matching-example](img/543f0b6a4908b9547b2c847e59096421.png) |

趋势是向上的，但要成为主流还有很长的路要走。

展望未来，我的计划只是写越来越多的灵丹妙药的代码，并越来越适应它。HackerRank 有 Elixir 作为环境，所以它是练习语法的一个很好的资源。接下来我想做的事情之一是开始在 [Phoenix](https://github.com/phoenixframework/phoenix) 中创建一些东西。

我在学习过程中使用的另一个资源是 Meetup 上的 [Elixir Toronto Meetup Group。](https://www.meetup.com/TorontoElixir/)

## 阅读资源

我们在读书俱乐部读的书叫做《灵药在行动》。一本非常好的书，详细介绍了整个语言及其特性。开始的时候很慢，但是当你开始思考语法时，很快就会变得非常有趣。

Elixir in Action

| ![pattern-matching-example](img/085105dd23f3e2ef2b518abf1e8b38e5.png) |

这是我开始写的另一本书，它更加平易近人。这是一本有趣的书，讲述了为什么 Elixir 是一门引人注目的语言的主要特征。这是一部激动人心的电影，因为它只是掠过了表面。

The Little Elixir & OTP Guidebook

| ![](img/429a2245fd82b5ba4c86fe00aed315d4.png) |

> 最初发布在[我的博客](https://blog.alec.coffee/elixir-better-programmer/)上。
> 
> 喜欢这个帖子？考虑给我买一杯咖啡来支持我写更多的东西。
> 
> 想收到有新帖子的季度邮件吗？[注册我的简讯](https://mailchi.mp/f91826b80eb3/alecbrunelleemailsignup)