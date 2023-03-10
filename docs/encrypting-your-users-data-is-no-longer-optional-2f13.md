# 加密用户数据不再是可选的

> 原文：<https://dev.to/tanker/encrypting-your-users-data-is-no-longer-optional-2f13>

*虽然平衡隐私和创新是当今技术发展中最关键的问题之一，但让我们来看看数据加密的机遇。*

今天的大多数数字企业代表他们的用户保存数据。这些资产对您的企业和用户都很有价值，这意味着它们是黑客的绝佳目标。保留他们的数据有着不可忽视的风险。虽然比尔盖茨在[他对 2018 年的回顾](https://www.gatesnotes.com/About-Bill-Gates/Year-in-Review-2018)中表示，2019 年技术发展最关键的领域之一是隐私和创新之间的平衡，但这确实是问自己“*如何保护我的用户数据的正确时间？*”，尤其是如果你在一家科技公司工作或领导一个数字项目。随着用户的信任而来的是巨大的责任，尤其是当更频繁的数据泄露对公司的品牌和[收入](https://newsroom.cisco.com/press-release-content?type=webcontent&articleId=1907897)产生破坏性后果的时候。

## **迫在眉睫的数据泄露，它们对用户信任和公司收入的破坏性影响。**

您在保存数据时面临的主要风险可以用术语*“数据泄露”*来描述。它包括部分或全部数据从您的服务器或云泄露给怀有恶意的个人。在过去的几年里，这些攻击变得越来越频繁，许多公司，如[雅虎](https://en.wikipedia.org/wiki/Yahoo!_data_breaches)、[易贝](https://www.forbes.com/sites/gordonkelly/2014/05/21/ebay-suffers-massive-security-breach-all-users-must-their-change-passwords/#28976a457492)或[索尼](https://en.wikipedia.org/wiki/Sony_Pictures_hack)，都经历过严重的信息泄露。

尽管如此，成为此类攻击目标的几率有多大？

正如思科首席安全官约翰·斯图尔特所说，数据泄露是必然会发生的:*“你最终会被攻击，不值得去想你不会被攻击。”*和*“最重要的是，不幸的是，在当今时代，没有一个组织能够免受数据泄露的影响。”*

让我们记住，你的用户的数据是一个信物，体现了你和他们之间的信任关系。

通过打破这种信任，您的组织发出了一个可怕的信息:您不考虑您的用户的福祉，并且您为会这样做的竞争对手敞开了大门。建立品牌需要很长时间，但摧毁只需一秒钟。

数据泄露的后果对任何企业来说都是灾难性的。这会导致客户寻找你产品的替代品，不订阅，甚至离开。

> 长话短说:失去对客户数据的控制，就意味着失去客户。

而且有越来越多的数据证明。2018 年 1 月，[思科隐私成熟度基准研究](https://www.cisco.com/c/dam/en_us/about/doing_business/trust-center/docs/privacy-maturity-benchmark-study-2018.pdf)显示，65%的组织报告由于客户的数据隐私问题导致销售周期延迟，平均延迟 7.8 周，并且组织的隐私成熟度水平与销售延迟持续时间相关。

[![Cisco 2018 Privacy Maturity Benchmark Study.](img/c677b4d5bb9057ef7e8d92b23e12d762.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JqTBhFBE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3012/1%2A7KA76iikSa9HPvAGw58jqw.png) 

<figure>

<figcaption>思科 2018 隐私成熟度基准研究。</figcaption>

</figure>

除了品牌和收入问题，立法已经发展到考虑隐私。随着 GDPR 在欧盟的实施和 AB 375 在加利福尼亚州的实施，数据泄露现在具有直接适用的法律后果和经济制裁。如果您的组织在欧盟境内处理用户的个人数据,[罚款框架](https://gdpr-info.eu/issues/fines-penalties/)最高可达 2000 万欧元，如果是企业，最高可达上一财年全球总营业额的 4 %,以较高者为准。

现在，您已经意识到数据泄露的破坏性后果，让我们来探索最佳的用户数据保护选项。

实际上，有两种选择:保护数据访问或保护数据本身。在 2019 年，如果你想保护你的用户的数据，有两个选择:保护数据访问或保护数据本身。

## **保护数据访问有其局限性:不可避免的人为错误。**

让我们一起回顾一下保护应用程序数据访问的必要步骤。

首先，您的代码必须遵循组织的安全准则。然后，它必须经过彻底的代码审查，最后由第三方进行审计。只要没有人为错误，这些过程就能保证您有一个安全的代码库。

除了您的软件很可能依赖于一些外部软件依赖、服务和硬件。遗憾的是，它们中的许多都不是按照与您的组织相同的安全期望级别来构建和配置的。对于很容易拥有不安全或令人困惑的默认配置的存储服务来说，最后这句话尤其正确。就连美国国家安全局也经历过这样的事故，红盘泄露，发生在亚马逊 S3 公共水桶上。

此外，即使在所有这些步骤之后，您也必须为紧急情况做好准备，通过快速建立的流程来查找和部署安全问题的修补程序。

总的来说，人为错误是无法避免的，依赖于应用程序中多个组件的安全性意味着更有可能发生错误，因为要保护的每一段额外的代码、依赖性或服务都会增加一层不确定性。

这就是加密的由来。

在过去的几十年里，加密已经成为保护最终用户数据隐私的最流行和最有效的方法之一。

## **说到数据本身的安全，投端到端加密一票。**

数据加密被定义为将数据转换成另一种形式或代码。数据加密后，只有拥有密钥或密码的人才能读取。

换句话说，加密数据可以确保只有授权用户才能读取数据。数据是加密存储的，从存储中的任何泄漏都只会泄漏加密的数据。为了访问明文数据，需要访问加密数据及其相关的加密密钥。在这方面，数据加密是防止数据泄露的强大屏障。

在实现数据加密的多种解决方案中，让我们评估一下最常见的几种:静态加密和端到端加密。

在应用程序中，许多利益相关者可以访问数据，主要是一些或所有可以访问应用程序服务器和存储的员工。

静态加密时，您的应用程序服务器通常会在将数据发送到存储设备(例如，数据库服务器或云中的服务)之前对其进行加密，并在从存储设备中检索数据之后对其进行解密。这意味着在存储级别发生的任何数据泄漏都不再是问题(例如，数据库保护不当或数据库备份被错误地公开)。但是，所有用户的数据仍然可以被应用服务器本身、部分或全部员工访问，因此容易受到攻击。

[![Example of using encryption at rest](img/73f9219f2b58307dd9e7d15e066b0c26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QQ4o8b1k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4000/1%2AmIKJw2_aVAEjyyHq1b5J-A.png) 

<figure>

<figcaption>为了向患者 Bob 发送机密医疗结果，Alice 医生正在使用一个虚拟的远程医疗应用程序，该应用程序采用静态加密。这里的问题是患者 Bob 的数据仍然可以在应用服务器和 KMS 中访问。</figcaption>

</figure>

如果发生针对您的应用服务器的攻击，明文数据会立即变得可访问。像 AWS KMS 这样的流行服务被广泛用于以这种方式实现加密。

## **端到端加密中和了服务器的漏洞，但它不是很用户友好。**

在端到端加密模式下，应用程序的员工无法访问明文数据，只有适当的用户才能访问。数据以加密的形式通过应用程序的多个层进行传输。应用程序服务器和存储只处理加密的数据，并且只有相关用户可以解密它。对应用服务器的攻击不会泄露任何东西，所以这就是为什么您必须一个一个地尝试攻击用户。

[![Example of using end-to-end encryption](img/18ef2be4a21162ea96688a933437c338.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QEhq42PV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4000/1%2AbDfDZ6UAAvYur_sJAfuGgQ.png) 

<figure>

<figcaption>在这里，爱丽丝博士使用的是相同的虚拟远程医疗应用程序，但这里的应用程序嵌入了端到端加密。患者 Bob 的医疗数据只能在用户的设备上访问，从而抵消了应用程序和服务器的攻击。</figcaption>

</figure>

虽然听起来很理想，但端到端加密的采用非常缓慢。主要的障碍是开发和设置此类技术的难度，以及对应用程序用户工作流的影响。

例如，PGP 用户体验的常见障碍是，用户必须手动保存和检索密钥才能拥有几个设备，或者只有通过为一组用户中的每个人再次加密才能与该组用户共享密钥。

显然，从用户体验的角度来看，这些问题是不可接受的，但是我们将在接下来的博文中阐述这一点:)

隐私和用户体验之间的平衡是一个难以置信的挑战，这也是我们 Tanker 选择解决的问题。我们努力提供尽可能用户友好的开源隐私解决方案。报名试试这里:[https://tanker.io/](https://tanker.io/)T3】

* * *

PS:本文由[loc Banet](https://dev.to/banetl)原创，发表在[油轮的媒介](https://medium.com/tanker-blog/why-encrypting-your-users-data-is-no-longer-an-option-17e6f2c6781b)上。由于您自己可能不在 Medium 上，我们在这里复制了它，以便您有机会在您的通知源中看到它。