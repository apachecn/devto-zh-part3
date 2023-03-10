# 接受调查的 Pipdig(如果你想退出，还有你的选择)

> 原文：<https://dev.to/bitofwp/pipdig-under-investigation-and-your-options-if-you-want-out-31h5>

如果你参与了 WordPress 社区，你可能听说过一个叫做 [pipdig](https://www.pipdig.co/) 的小型商业主题公司，它发现自己处于一些非常严重的争议的中心。

虽然丑闻爆发已经一周多一点了，但一直在使用 pipdig 主题或其任何一个插件的人(*特别是 pipdig Power Pack (p3* ))可能会发现自己不知道下一步该做什么。

## 这一切是如何开始的

2019 年 3 月 29 日，Wordfence 威胁分析师米奇·文斯特拉[发表了一份关于小型主题公司 pipdig 的报告](https://www.wordfence.com/blog/2019/03/peculiar-php-present-in-popular-pipdig-power-pack-plugin/)，该公司也恰好提供托管服务。这篇报道震惊了 WordPress 社区，揭露了 pipdig 团队的一些非常严重的(*和可能非法的*)行为。

简而言之，pipdig 被发现使用其高级插件 pipdig Power Pack (p3)来创建隐藏的后门，使用插件中内置的混淆代码，这些代码可用于执行以下操作:

*   让 pipdig 的团队能够使用 p3 插件授予自己网站的管理员权限
*   重置管理员密码，以便轻松访问属于他人的网站
*   使用 p3 远程删除整个网站数据库

换句话说，Wordfence 发现任何使用 pipdig Power Pack (p3)插件的网站都有被恶意利用的风险，而网站所有者对此一无所知。

更糟糕的是，文斯特拉的报告指出，“由于这些后门的性质，远程调用 Pipdig 的服务器，而不是监听传入的请求，入站防火墙规则无法阻止这些行为。”

这意味着，即使你采取了适当的措施来保护你的 WordPress 站点免受外部威胁，你也无法阻止 pipdig 使用他们的插件闯入并使用你的站点。

### 但是等等，还有更多…

与此同时，Wordfence 正在调查 pipdig 后门问题，来自英国的自由网络开发者 Jem Turner[公布了调查结果](https://www.jemjabella.co.uk/2019/security-alert-pipdig-insecure-ddosing-competitors/)pip dig 正在利用客户网站对竞争主题公司进行小规模 DDoS 攻击。另外，她发现 pipdig 在用户不知情的情况下收集用户数据*。*

事实上，她对 pipdig Power Pack 插件冗长且颇具技术性的分析揭示了 pipdig 过去是/现在是:

*   利用客户的服务器对竞争对手进行 DDoS 攻击
*   操纵博主的内容，将竞争对手 WordPress 迁移服务的链接改为指向 pipdig 网站
*   未经许可从人们的网站上收集数据(*这直接违反了 GDPR*
*   使用收集到的数据重置管理员密码并访问博客站点
*   实现内置的“kill switch”来删除所有数据库表
*   未经许可禁用 pipdig 认为不必要的其他插件
*   隐藏 WordPress 核心中的管理通知和元框，以及仪表板中的其他插件

这家英国的网络开发商最初是如何发现这些事情的呢？

她的一个客户找到她寻求帮助，因为她注意到她的网站，运行一个 pipdig 主题，表现得很奇怪。因此，特纳主动去调查了一些事情(就像任何一个优秀的 web 开发人员会做的那样),她遇到的事情比她预料的要多得多，声称她“从未见过这样的事情”

## 皮迪格对指责的反应

当然，任何一家因做事方式不当而受到抨击的公司都会以这样或那样的方式做出反应。pipdig 也不例外。

首先，pipdig 团队很快发布了 pipdig Power Pack 版本 4.8.0，删除了大部分有问题的代码。然而，任何使用 pipdig 主题的客户，以及没有将他们的插件更新到版本 4.8.0 的 p3 插件*，仍然面临数据库删除、从他们的站点产生的 DDoS 攻击等风险。*

除此之外，据我们所知，p3 4.8.0 仍然能够在未经用户许可的情况下，在他们的网站上关闭插件。由于一些自动禁用的插件和 admin_init 一起被停用，所以没有办法重新激活它们。

同样有趣的是，在 2019 年 3 月 31 日，当所有这一切都暴露在公众面前时，pipdig 删除了它的公共 Bitbucket 存储库，并用一个“干净的”取代了它。

让我们来看看在删除之前被查封的[克隆库](https://www.wordfence.com/blog/2019/04/pipdig-update-dishonest-denials-erased-evidence-and-ongoing-offenses/) Wordfence，它可以追溯到 2015 年:

[![old repository](img/3ce09fa71b8314a5da7c611e18c8ebb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0s4g02hV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.bitofwp.com/wp-content/uploads/2019/04/old-repository.png)

这里有一个“新的和干净的”存储库:

[![clean respository](img/4c4e0b9bad82bb09269fde386cda2382.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ICvFlKeX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.bitofwp.com/wp-content/uploads/2019/04/clean-respository.png)

对许多人来说，这清楚地表明 pipdig 试图隐藏一些东西，这对他们试图坚持的任何辩护都不是好兆头。

我们不要忘记 pipdig 处理客户全额退款请求的方式。没有人愿意为一个可以独自摧毁他们的网站并被恶意劫持的产品付费。但是 pipdig 坚持他们的“不退款”政策，尽管用户强烈反对。

他们甚至公开宣称客户的指控和 Wordfence 的发现是“虚假的、扭曲的或耸人听闻的”

[![pipdig no refund response](img/6e1dd9d3efd8146e2059391699a075e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MNZE278k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.bitofwp.com/wp-content/uploads/2019/04/pipdig-no-refund-response.png)

### 皮迪格的公开辩护

pipdig 创意总监 Phil Clothier 发布了一份公开回应，回应他所称的指控和谣言，以下是他要说的部分内容:

*   pipdig 不 DDoS 竞争对手，“杀死”网站或使用 pipdig 电源包插件破坏网站
*   特纳在她的分析中提到的内置“杀死开关”是一个“…插件旧版本中的功能，可以用来将网站重置回默认设置…这个功能已经被挖掘出来，并被贴上了“杀死开关”的标签，以便对我们产生最大的负面影响。”
*   “kill switch”功能自 2018 年 7 月才出现在 p3 插件中， [Wordfence 发现为假](https://web.archive.org/web/20190331195426/https:/bitbucket.org/pipdig/p3/commits/edc47824200e15d64cab7270debc4a0526a8d323) ( *第一例数据库删除发生在 2017 年 11 月*
*   用户是 DDoS 攻击的罪魁祸首，因为他们不得不添加竞争对手自己网站的 URL

尽管 pipdig 公开否认了所有关于他们恶意活动的指控，但事实是无法掩盖的。

因此，pipdig 已被报告给互联网主管部门，并正在接受全面调查。甚至他们自己的主机服务提供商也采取了积极主动的方法，禁用在他们自己的单独调查中发现的恶意代码。

[![pipdig hosting disabling malicious code](img/18a948e195af97d0de39acaf70c85fe6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8d5b45pD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.bitofwp.com/wp-content/uploads/2019/04/pipdig-hosting-disabling-malicious-code.png)

并且 [Wordfence 对整个情况有这样的说法](https://www.wordfence.com/blog/2019/04/pipdig-update-dishonest-denials-erased-evidence-and-ongoing-offenses/)(*修改了他们最初的建议，以保持谨慎，但还没有完全摆脱 pip dig*):

> “Wordfence 建议从你的网站上删除所有 Pipdig 内容，包括 WordPress 和 Blogger。Pipdig 已经表现出滥用用户资源来执行不道德的、潜在非法的活动的意愿。此外，他们一再否认确凿的证据，并随后试图隐瞒，使我们今后无法信任他们。"

## 作为一个博主，下一步是什么？

好吧，所以你知道 pipdig 肯定参与了一些不正当的策略，你不想再参与其中了。

但是作为一个 pipdig 主题所有者和 p3 插件用户，你应该做些什么呢？

对许多人来说，改变主题是一个充满压力的考验。当这样的事情发生时，时间、金钱、技能和对工作主题的热爱都是需要考虑的事情。然而，鉴于他们最近的行为，pipdig 的客户害怕继续使用 pipdig。

这就是为什么我们在这里提供一些帮助，如果你想摆脱成为一个 pipdig 客户:

*   **申请退款:**如果你在过去 180 天内购买了一个 pipdig 主题，尽管有“不退款”的政策，还是通过电子邮件申请退款。如果他们忽视或拒绝你的请求，向 PayPal 或你的信用卡/银行提供商提出。现有的消费者保护法和虚假和误导行为的证据可能足以让你的钱回来。
*   **[p3 Neutralizer 插件](https://github.com/markjaquith/p3-neutraliser) :** 当你决定下一步采取什么措施时，使用 p3 Neutralizer 插件，它可以防止 pipdig Power Pack 插件或你的 pipdig 主题更新或呼叫可能损害你的网站的恶意 pipdig URLs。
*   寻找新主题:我们知道你可能会喜欢你的 pipdig 主题。但是使用 WordPress 的美妙之处在于，有大量可靠且无恶意的 WordPress 主题可以满足任何预算。查看 WordPress 知识库中的免费主题，这些主题总是使用最高标准进行编码。或者，看看[这个有用的资源](https://tiawood.com/pipdig-themes-how-to-find-alternatives/)的另类 pipdig 主题。
*   获得新的虚拟主机:如果你碰巧使用 pipdig 作为你的虚拟主机提供商，把他们也处理掉。与一家竭尽全力破坏客户网站并掩盖其踪迹的公司做生意，不是你应该将你的网站委托给的公司。

如果你没有时间或技能来摆脱 pipdig 主题、插件或托管服务，并且想要立即从任何可能正在你的网站上发生的恶意活动中解脱*现在*，在 BitofWP 这里从我们[获得 24 小时恶意软件紧急救济。](https://bitofwp.com/)

### 我们在 BitofWP 的目标是:

*   在 24 小时或更短时间内清理你的网站
*   清除所有恶意软件，同时使用人工(*而不是机器*)监控每一步
*   提供三个月的清洁现场保证(*如果问题在三个月内仍然存在，我们将再次免费清洁您的现场*
*   升级所有 WordPress 核心文件、主题和插件，确保所有恶意软件和不良文件都被删除
*   清除所有恶意软件重定向
*   移除网站后门(*像 pipdig 在他们的 p3 插件*中创建的那些)
*   清除你网站上的所有制药攻击
*   帮助你建立一个新的主题，并让以前停用的插件重新启动和运行

我们甚至会帮助您将您的网站从 pidig 托管迁移到另一个托管提供商—*零停机时间*——因此您再也不用担心成为 pidig 恶意方式的受害者。

## 最后的想法

这个事件向 WordPress 社区展示了 WordPress 特有的主题和插件是多么的脆弱，以及人们对于那些想要利用他们的客户的人的保护是多么的少。这就是为什么总是推荐使用经过审查和验证、定期更新、100%透明的 WordPress 软件。

然而，这个事件也显示了当不好的事情发生时，WordPress 社区是多么的有帮助。

WordPress 以团结和帮助各种技能水平的人而自豪，他们有不同的需求，不问任何问题。我们可以从已经发表的让人们意识到这个问题的不同文章中看到，pipdig 对指控的反应的更新，以及社交媒体和博客评论上的精彩 WordPress 用户主动帮助其他想要成为 pipdig 客户的人，这个社区正在做它最擅长的事情——*互相帮助*。

*汉斯-彼得·高斯特在 Unsplash 上拍摄的照片*

被调查的帖子[pidig(如果你想退出，还有你的选择)](https://bitofwp.com/security/pipdig-under-investigation-and-your-options-if-you-want-out/)首先出现在由 BitofWP 提供的 [WordPress 支持服务上。](https://bitofwp.com)