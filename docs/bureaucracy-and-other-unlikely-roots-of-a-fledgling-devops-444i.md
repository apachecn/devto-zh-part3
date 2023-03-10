# 官僚主义和其他羽翼未丰的 DevOps 不太可能的根源

> 原文：<https://dev.to/pcloudy/bureaucracy-and-other-unlikely-roots-of-a-fledgling-devops-444i>

[![](img/d2d82a18dbc15745922b3b7fff791bea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RbRAIvCN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcloudy.com/wp-content/uploads/2018/11/devops-testing.png)

大多数 DevOps 模式是详尽的，并且需要一些强有力的承诺，在时间和精力方面，这就是为什么很多时候人们不愿意将 DevOps 引入他们的团队。因为当您尝试将 DevOps 改装到您的设置中时，通常需要对您的工作方式和工作流程进行大量更改。如果你这么做时没有认真考虑后果，那就更是如此。那么，我们如何创建一个连续的交付系统呢？我们如何构建一个既健壮又足够灵活的系统来适应需要的变化呢？

DevOps 面临着独特的挑战。很难将 DevOps 改造成一个正在运行的系统，这样做不会直接带来收入。因此，在一个正在运行的启动设置中，我们根本无法想象停止一切，专门花时间在这上面，然后回到我们的其他任务。但这并不意味着我们不能有一个功能性的 DevOps，这只是意味着我们必须从 MVP(最小可行产品)到 MVP，早期的 MVP 可能看起来很尴尬，但首先证明概念然后继续构建概念是有用的。否则，你可能会得到一个复杂的解决方案，它可能有效，但可能不是你所需要的。

我已经谈过我们的思考过程，我们反对盲目地重新发明车轮或改装别人的车轮到我们的车上。DevOps 也是如此，除非有明显的事情，否则我们从来没有为 DevOps 或 DevOps 流程做任何前期开发，而是一步一步地自动化我们能自动化的部分，官僚化我们不能自动化的部分。然后回去把失败的 MVP 丢掉，把已经通过的正式化。一如既往地坚持下去。因为最重要的是，DevOps 是敏捷态度的结果。如果您在构建 DevOps 时没有考虑敏捷，那么您将会得到一堆随机的自动化工具，而不是一个活生生的交付系统。

所以，这里是我们在进化过程中的不同时期使用的一些策略，来实现一个成功的时钟。

**表格**

他们中最官僚的。表格是官僚机构的终极标志。无论何时你想从任何一个官僚机构得到任何服务，你都必须填写一张表格。这看起来一点也不酷。然而，无论是在线还是线下，它在世界各地都依然强劲，这是有原因的。表单的主要好处是您可以以正确的格式获得所有需要的数据，并且很容易发现是否缺少任何必需的数据。这很有帮助，因为数据丢失可以在一开始就解决，而不是在以后。当一组人需要另一组人提供的服务，而另一组人的工作与第一组人完全不同时，我们就会使用表单。例如，当开发人员希望部署团队在某个地方部署某些东西时(我们的任何测试环境或阶段或产品)，现在开发人员可以做到这一点。但是部署团队拥有开发人员所没有的口令和对试运行和生产设置的访问权，并且他们还拥有开发团队可能不知道的最佳实践。他们面临的一个问题是，每个开发人员对于如何将他们的产品推向生产都有不同的想法。对于一个领域的开发人员来说，仅仅复制代码就足够了，其他人需要修改二进制文件，还有一些人希望修改二进制文件和配置。一些进程需要重启，而另一些不需要。一些需要重启服务器，而另一些不需要。对于一些开发者来说，运行一个更新脚本就足够了，而对于其他人来说，还需要做一些其他的事情。很难跟踪这些。一个解决方案是让部署自动化。这就是我们最终到达的地方。但这不是一日之功，在完成之前，我们不可能停止部署。

因此，我们需要确保在提出请求时，部署团队拥有正确的信息，而不是团队已经完成了一半的部署。我们试验了电子邮件，但是大多数开发者写的是他们认为相关的东西，而不是真正相关的东西。所以我们要求开发者提出所有需要的东西。但这也不是太明确的要求。许多人仍然达不到要求。所以我们创建了一个表单。最大的优势是开发人员现在不会意外错过任何重要的信息。当表单明确要求信息时，很难遗漏信息。所以现在开发者填写所有的信息，甚至是他们认为明显或不相关的信息。部署团队现在有权像官僚机构一样拒绝未填写数据的表单。这就像魔法一样，我们的部署变得不那么容易出错，开发和部署团队争论他们所说的话是什么意思的情况也少了很多。添加部署表单的直接结果是，我们的部署现在更少被破坏。

**清单**

正是这些清单最终让我们意识到表单也可以工作。清单是很多人选择的零脑工具。这种情况已经持续了将近一个世纪。或许更多。然而，清单的力量经常被低估或忽视。你需要读一读清单宣言——阿图尔·加万德(Atul Gawande)写的，以理解清单是多么重要和有价值，以及忽视它们是多么疯狂。有很多事情我们都没有做对，或者忽略了小的或明显的步骤。不管我们怎么努力，我们还是会一遍又一遍地重复同样的错误。通常，我们的带宽太紧，无法深入研究这些问题，找出问题所在。负责人会说，他们已经完成了应该完成的每一步。显然，他们不认为自己做错了。因为如果他们认为他们做的是错的，他们第一次就不会做了。没有人喜欢重复做同样的事情。当然，许多这样的过程最终实现了自动化，但是在它们实现之前，需要有一个解决方案。也不是所有的事情都可以自动化。所以我们为每一个重复的过程创建了清单。并把它们放在显眼和容易找到的地方(物理上和在我们的内部网中),这也像魔术一样起作用。很快，重复过程中的错误数量大幅下降。一旦错误减少，处理这些任务的人就有更多的时间和带宽，他们有时间来修复他们的流程并实现自动化。这样，手动过程不再需要由人来完成。这使我们进入下一步。

**微型自动装置**

虽然很久以前我们就清楚在我们的日常工作中需要大量的自动化，但是有一件事并不允许我们立即这样做。那就是自动化需要时间，通常客户的功能有最高的优先级和最低的可用时间。那么我们什么时候会花时间在自动挡上呢？一段时间以来，我们只是推迟了自动化工作，认为在当前的一系列问题完成后，我们将有时间从事自动化工作。然而，我们很快意识到，这是一个海市蜃楼，作为初创公司，为这种事情获得带宽不会很快成为现实。但是，这是否意味着我们放弃了它和它将带来的好处呢？不，我们所做的是，我们花时间确保我们能自动化的任何部分，我们都会自动化。并确保我们继续使用它，除非它完全无法使用。

我们确保我们开始只自动化很小的部分。我称这个过程为微型自动化。顾名思义，我们一次只自动化一点点。我们还确保我们自动化的每一点都是明智的，(具体的、可衡量的、可实现的、现实的和有时限的)。此外，我们将每一部分都做得足够小，这样一两个人就可以兼职完成，他们非常清楚主要功能显然具有优先权。

这确保了随着时间的推移，我们自动化了越来越多的过程，比如我们的构建系统、部署过程、自动化测试等等。一点一点。然而，随着时间的推移，我们取得了很大成就。最后，在这几个月之后，我们回顾过去，惊叹我们实际上取得了多大的成就。正如一句古老的谚语所说，我们赢得了比赛，靠的是缓慢和稳定。

我还想谈更多这样的事情，但我想仅限于我们试图谈论的官僚主义问题。我们希望确保我们的重点是解决 [pCloudy](https://www.pcloudy.com) 应该为我们的客户解决的问题，同时，我们确保我们也为未来做好准备，并越来越准备好作为一个组织进一步扩展。欢迎您的评论和见解。我想在评论区讨论一下这个话题。