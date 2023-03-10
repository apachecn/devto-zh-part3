# 现成的云平台与基础设施即代码的 DIY

> 原文：<https://dev.to/duplocloud/off-the-shelf-cloud-platforms-vs-diy-with-infrastructure-as-code-eg6>

在这篇博文中，我们比较了两种流行的云基础设施管理方法。首先是我们广泛分类的基础设施即代码，工程师使用编程\脚本语言来构建一组脚本，以在云平台上实现所需的拓扑结构。地形，云形成，厨师，木偶，Ansible 是一些受欢迎的。这项技术由编写脚本的语言和运行脚本的控制器组成。一旦对结果满意，用户会将脚本保存在代码库中。随后，如果要进行更改，那么将编辑文件并重复相同的过程。

第二类是“云协调器”或“平台”。这通常是对原生云 API 的抽象。这将作为 web 服务与用户交互，用户将连接到该服务(通过 UI 或 API ),并在该 web 服务本身中构建云拓扑。构建的拓扑将由 orchestrator 应用并保存在其自己的数据库中。用户不需要显式保存配置。当需要更新时，用户将再次登录系统并进行更改。

对于较小规模的用例，平台可能太重。但在规模上，前者变成了一个内部平台。在这篇博客中，我们认为对于更大规模的基础设施，更好的策略是使用现成的平台，在需要定制时，可以使用基础设施即代码脚本来增强。像 facebook、网飞这样的超大规模数据中心是另一回事，本博客中没有任何内容适用于它们。

**“长时间运行上下文”**

基于平台的方法提供的基本价值就是我们所说的“长期运行环境”。通常人们称之为“项目”或“租户”。上下文可以映射为一个应用程序或环境，如演示、测试、生产或开发人员沙箱等。当对拓扑进行更新时，用户总是在这个上下文中操作。在将更新应用到云之前，平台会将更新保存在自己的数据库中。人们总是可以保证这个数据库中的内容就是应用到云中的内容。

在基础结构作为代码的方法中，这样的上下文不是本地提供的，而是留给用户。通常，这将转化为类似“哪个脚本需要为哪个上下文运行”的内容，或者可能是代码库中代表给定“租户”或“项目”的配置的“文件夹”。将上下文定义为代码集合更困难，因为许多脚本可能在租户之间是通用的。因此，最有可能的是，这取决于开发人员对代码库的理解。

平台是解决问题的更具声明性的方法，因为它不需要或很少需要编码，因为系统将基于意图生成代码，而不需要低级实现细节的知识，而在编写脚本的情况下，任何改变都需要对代码库的良好理解，尤其是在大规模操作时。用户可以在几天后返回并登录到相同的上下文，并从他离开的地方继续，而不必深入研究代码来理解之前做了什么。

## **代码库和应用于云的内容之间的差异**

两者之间的第二个基本区别是，基础设施即代码是一个多步骤的过程，即编写脚本、运行脚本并将其合并到回购中，而平台是一个一步的过程，即登录到上下文并进行更改。在编写脚本期间，可能有人会更新脚本，运行相同的脚本来进行拓扑更改，但他可能会忘记或推迟在存储库中保存。与此同时，另一名工程师对他所在的拓扑结构的代码库进行了修改并将其合并。现在，由于两个用例共享许多代码，第一个开发人员可能会发现自己处于冲突中，即使通过合并代码解决了冲突，也会使他处于这样一种情况，即在云中运行的不是他在回购中拥有的。所以现在他不得不重新运行合并后的代码来验证相同的代码，尽管这可能会导致回归。因此，为了避免风险，我们现在需要在 QA 环境中测试脚本。

## **所有“其他”的东西**

脚本工具将支持部署，但运行云软件的基础设施还有更多的内容。我们需要一种应用程序配置机制，一种收集和隔离每个应用程序的日志和指标、监控运行状况并发出警报、审计跟踪的方法，一种管理用户访问基础架构的身份认证系统。有几种工具可以用来解决这些单独的问题，但是它们需要放在一起并集成到应用程序环境中。Kubernetes、splunk、cloudwatch、signalfx、sentry、Elk、oauth 提供商都是这些工具的例子。但如果它们要以合理的规模运作，就需要一个连贯的“平台”将所有这些整合在一起。这就引出了我们的下一点。

## **“大量”基础设施即代码基本上是自主开发的云平台**

在与许多工程师交谈时，我们听到这样一种观点，即基础设施即代码与常规编程语言(如 go、java 和 python)的 bash 脚本相结合，提供了克服上述挑战的所有方法。此外，确实有重复使用像 Kubernetes，詹金斯等很多作品。我当然同意，它的“代码”可以构建一切。但实际上，你可以自己构建同样的平台。为什么不从现有平台开始，通过脚本添加定制。同样，相对于 ROI 的“批次”的定义是特定于业务的。如果你有大量的资金或上市时间，或者有像脸书和网飞这样的大规模用例，那么情况就不同了。

我们听到的第二个论点是，基础设施即代码最灵活，允许深度定制，而在平台中，人们可能必须等待供应商提供相同的支持。我认为，随着汽车自动驾驶技术的进步，平台越来越好，有很好的机器生成技术来满足大多数(如果不是所有)用例。另外，一个好的平台不会阻止用户通过脚本工具定制超出其范围的部分。一个设计良好的平台应该提供合适的钩子来使用在平台之外编写的脚本。因此，在我看来，这种观点并不能证明为大多数标准任务建立代码库是正确的。

## **【没有适合我们需求的平台】**

这也是一个常见的论点。这意味着需要一个好的平台来解决一个巨大市场中的普遍问题。从我们个人的角度来看，这都是好的。通过 DuploCloud，我们相信已经构建了一个很好的平台，它可以处理大多数用例，同时能够集成在系统外部创建和管理的策略。

## **“圣马特奥线！”**

构建国产平台的一个隐藏的论点是，这是一个非常酷的项目，尤其是如果工程师来自系统背景。我们住在湾区，发现一个很有意思的趋势(不是一概而论！)在与圣马特奥北部和南部的顾客交谈时。当我们与南方公司的基础设施工程师交谈时，我们发现他们有更强烈的愿望在内部构建平台，他们也很清楚他们正在为各自的组织构建一个“平台”，而不是像他们所说的那样“编写脚本”。定制是反对现成工具的常见理由。混合云和内部部署是一个非常重要的使用案例。像 K8，领事等开源组件是常见的，因此断言车轮没有被重新发明。然而团队的规模和分配给解决方案的时间是巨大的。以我的偏见，我看不到投资回报率或定制的论点。在某些情况下，对构建平台的关注掩盖了公司应该销售的核心业务产品。

构建国产平台的一个隐藏的论点是，这是一个非常酷的项目，尤其是如果工程师来自系统背景。我们住在湾区，发现一个很有意思的趋势(不是一概而论！)在与圣马特奥北部和南部的顾客交谈时。当我们与南方公司的基础设施工程师交谈时，我们发现他们有更强烈的愿望在内部构建平台，他们也很清楚他们正在为各自的组织构建一个“平台”，而不是像他们所说的那样“编写脚本”。定制是反对现成工具的常见理由。混合云和内部部署是一个非常重要的使用案例。像 K8，领事等开源组件是常见的，因此断言车轮没有被重新发明。然而团队的规模和分配给解决方案的时间是巨大的。以我的偏见，我看不到投资回报率或定制的论点。在某些情况下，对构建平台的关注掩盖了公司应该销售的核心业务产品。

在圣马特奥北部，我们发现大多数是原生云应用程序，而不是混合云应用程序。核心天赋是全栈。商业的本质是 SAAS。应用程序使用了太多的原生云软件(S3、Dyamo、Sqs、Sns ),很难做到混合。他们很乐意通过 API\UI 将容器交给 AWS ECS 来部署。他们在部署或学习 Kubernetes 的过程中找不到乐趣。因此，内部定制的趋势和深度要小得多。

在圣马特奥北部，我们发现大多数是原生云应用程序，而不是混合云应用程序。核心天赋是全栈。商业的本质是 SAAS。应用程序使用了太多的原生云软件(S3、Dyamo、Sqs、Sns ),很难做到混合。他们很乐意通过 API\UI 将容器交给 AWS ECS 来部署。他们在部署或学习 Kubernetes 的过程中找不到乐趣。因此，内部定制的趋势和深度要小得多。

多少次多少人会写同样的代码达到同样的用途。上市时间最终会占上风。

帖子[现成的云平台与基础设施即代码的 DIY](https://duplocloud.com/blog/off-the-shelf-cloud-platforms-vs-diy-with-infrastructure-as-code/)首先出现在 [DuploCloud](https://duplocloud.com) 上。