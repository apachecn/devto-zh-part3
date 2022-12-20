# 赫兹、埃森哲和指责游戏

> 原文：<https://dev.to/browserlondon/hertz-accenture-and-the-blame-game-280h>

上周有大量报道称[赫兹对跨国咨询和外包公司埃森哲提起诉讼](https://www.theregister.co.uk/2019/04/23/hertz_accenture_lawsuit/)，指控其未能履行合同，为这家汽车租赁巨头及其多个子品牌提供新网站。

诉讼中引用的埃森哲所有作品的总成本？令人垂涎的 3200 万美元。不，这不是印刷错误，这确实是赫兹声称到目前为止花了多少钱来建立网站。

一定是个很棒的网站，对吧？好吧，不要忘乎所以。赫兹声称，它聘请埃森哲制作了一个与设备无关、响应迅速的网站，由几个组件组成，不得不说，没有一个组件看起来特别新颖。Hertz 还声称，它指定构建应该使用一个通用的核心[可重用库](https://www.browserlondon.com/blog/2015/02/02/application-design-are-pattern-libraries-the-new-deliverable/)。这样做的目的是使支持赫兹在所有国家经营的所有品牌变得简单。

到目前为止，一切都很普通，但诉讼声称埃森哲只是忽略了这些(以及其他一些)要求，而是只为北美业务开发了一个网站，没有考虑到[可重用组件](https://www.browserlondon.com/blog/2017/04/25/atomic-design-in-practice-does-it-work/)，代码充满了安全和性能漏洞。

有趣的是，埃森哲(在撰写本报告时)似乎愿意打官司，所以情况可能不像赫兹所说的那么明朗。

## 利益相关者的参与是双向的

不管是哪种情况，我看到的关于这个故事的大部分报道都集中在工程的纯粹成本上，并提出了许多很好的观点，表明埃森哲等公司的商业模式是在客户已经做出大量承诺的情况下故意抬高费用。除了最初的[发现工作](https://www.browserlondon.com/services/research-analysis/)的 700 万美元之外，该诉讼没有说明商定的合同费用是多少，但它详细说明了一旦被捆绑，埃森哲如何不断为价值可疑的修复或新技术向赫兹收费。

然而，对我来说最突出的是这种情况的另一方面。在停工之前，赫兹的花费是如何膨胀到 3200 万美元的？

在我看来，这凸显了许多企业在从事不属于其核心能力的大型项目时似乎会遇到的一个基本问题——即他们对项目日常运营的参与。毕竟，直到赫兹的高管询问平板电脑视图的进展，埃森哲才意识到，赫兹要求它做的许多事情，埃森哲根本没有做。

我已经阅读了[轶事证据](https://news.ycombinator.com/item?id=19740706),事实上，在开始与埃森哲合作这个项目之前，赫兹解雇了大部分内部数字和开发人才，将全部控制权移交给埃森哲。在我看来，这是它的第一个(如果不是最大的)错误。

## 你不能外包责任

我不会重蹈“不要外包，内部完成”的老路，因为在很多情况下外包是合适的。这不是零和游戏。如果一家公司没有合适的文化、人才或规模来管理一个项目，他们将需要外界的帮助。然而，关键是像这样的大型项目需要与客户积极的日常合作。Hertz 应该有自己的内部人才参与进来，帮助指导项目并提供主动监督，这应该是 Hertz 的那个人或团队的主要工作职能。

以这种方式将客户包含在[敏捷过程](https://www.browserlondon.com/blog/2015/05/28/the-agile-process-explained-for-non-technical-people/)中，将会给各方带来更好的结果。客户不仅可以保持参与并比其他可能的情况更早地发现误解或其他问题，而且开发团队可以访问只有客户拥有的领域知识。

根据诉讼，在这种情况下，这种情况并没有发生。销售演示给赫兹留下了深刻的印象，他们决定信任埃森哲也担任产品负责人。

我坚信，客户所有权是成功数字项目的一个基本驱动力，如果不是*。此外，项目越大，参与的需求就越大。我们一次又一次地在移交给大财团的大项目中看到这种情况，我觉得，缺乏与客户高管*和最终用户*的互动是项目失败的一个重要因素，更常见的是，过度超支。*

 *## 数字基础设施也需要维护

另一个值得提出的观点是，如果你预计在一个项目上花费数百万，那么这个项目*很可能永远不会结束*。我的意思不是说它会在时间和成本上超支，而是说如此大的项目可能是重要的，而且可能是业务关键的——在这个案例中很明显就是这样。

因此，随着时间的推移，它需要得到[支持和扩展](https://www.browserlondon.com/services/continuous-improvement-support/),以响应业务和市场不断变化的需求，就像任何传统的基础设施(如工厂)一样。当 Hertz 网站交付时，Accenture 是否打算简单地放下文件，从此不再参与？不太可能。有人需要维护和[继续适应和改进](https://dev.to/browserlondon/the-role-of-continuous-development-in-web-design-35fd)这个系统，所以作为一个客户，你是在一个持续的基础上雇佣一个长期合作伙伴。

我不打算把所有的责任都推到赫兹身上；明明已经被冤枉了。然而，在我看来，该公司的高级团队过于信任，没有给予该项目应有的重视。项目所有者既需要自己的内部专业知识，也需要合作伙伴带来的专业知识。只有当这两个知识库协同工作时，您才能规划有效的未来路线图，提供持续的开发服务，并不断审查您的技术和战略计划。这种方法应该从第一天起就融入到关系中。

从这个角度来看，在我看来，Hertz 的主管根本不明白网站项目不可避免地会超过最初的构建。如果他们意识到了这一点，他们就会明白，他们这一方至少需要一些全职关注项目的人才。该公司本不应该像据称的那样让这位人才离开。

那么，埃森哲应该承担所有的责任吗？依我拙见，没有。在任何人键入一行有错误的代码之前，赫兹就把自己置于了一个站不住脚的位置。我希望我们吸取了教训，但我怀疑我们会继续看到类似的故事。

* * *

赫兹、埃森哲和指责游戏的帖子首先出现在伦敦的 T2 浏览器上。*