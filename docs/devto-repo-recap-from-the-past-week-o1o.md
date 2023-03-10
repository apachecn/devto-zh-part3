# 开发人员对上周的回购进行总结

> 原文：<https://dev.to/devteam/devto-repo-recap-from-the-past-week-o1o>

欢迎回到另一个回购回顾，我们将讨论上周对 dev.to 库和[iOS 回购](https://github.com/thepracticaldev/dev-ios)的贡献[。这一期涵盖 2 月 3 日至 2 月 9 日。](https://github.com/thepracticaldev/dev.to)

# 特性

*   [@ben](https://dev.to/ben) 做了不少性能提升。请查看以下内容:

    *   [删除附加查询](https://github.com/thepracticaldev/dev.to/pull/1717)
    *   [修改内存硬编码限制](https://github.com/thepracticaldev/dev.to/pull/1718)
    *   [Ping 评论以预热新评论树](https://github.com/thepracticaldev/dev.to/pull/1755)
    *   [提高附加内容框和标签索引的性能](https://github.com/thepracticaldev/dev.to/pull/1750)
*   [@link2twenty](https://dev.to/link2twenty) 添加了另一个 CSS 变量-主题-容器-颜色，作为他继续黑暗主题努力的一部分。谢谢安德鲁。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1731)
*   [@韵](https://dev.to/rhymes)给徽章图片增加了`title`属性。谢谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1738)
*   [@韵脚](https://dev.to/rhymes)做了 PR 把我们的 Ruby 版本升级到 2.6.1。再次感谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1711)

# Bug 修复/其他贡献

*   @maestromac 重构了我们的测试，并使我们的测试更可靠地通过。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1693)
*   @jpachitas 解决了一个小 bug，在这个 bug 中，从一个中等大小的 RSS 源中提取的带有 gists 的帖子有时会失败。谢谢， [@jpachitas](https://dev.to/jpachitas) ！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1735)
*   [@韵](https://dev.to/rhymes)移除了重复的哈士奇配置。Husky 是我们用来在提交文件之前检查文件的工具。谢谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1712)
*   [@glennmen](https://dev.to/glennmen) 增加了自动奖励 3 周年徽章的算法。谢谢， [@glennmen](https://dev.to/glennmen) ！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1732)
*   [@aligoren](https://dev.to/aligoren) 修复了一个 CSS 小问题,“编辑文章”按钮离个人资料图片太近。谢谢@阿里戈伦！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1749)
*   @vaidehijoshi 修复了我们的天光徽章，使其指向现在公开的网址！更多细节在我们的帖子下面，关于我们如何使用天光。谢谢你，维德希！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1680)

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## 开发社区正在通过 Skylight 开源其性能指标

### 开发团队的 Ben Hal pern 1919 年 2 月 7 日 3 分钟阅读

#meta #opensource #showdev #rails](/devteam/dev-community-is-open-sourcing-its-performance-metrics-via-skylight-221m)

*   [@rhymes](https://dev.to/rhymes) 更新了我们的 Rubocop 配置以匹配我们的 Ruby 版本，并在我们的预提交钩子中使用安全自动更正。谢谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1713)
*   我们用新的内容政策更新了我们的条款。请查看[条款页面](https://dev.to/terms)或下面的 PR。公关由[@彼得](https://dev.to/peter):

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1751)
*   [@ben](https://dev.to/ben) 对我们如何通过 RSS 获取新帖子做了一些优化:

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1752)
*   [@ben](https://dev.to/ben) 还增加了两个新的时间戳列以避免额外的获取:`profile_updated_at`和`github_repos_updated_at`。下面的 PR:

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1754)
*   [@lightalloy](https://dev.to/lightalloy) 修复了一个一直影响我们后台作业和内存使用的 bug，并开始了将我们的作业转移到 Rails 的 ActiveJob 中的过程。谢谢安娜。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1699)
*   [@seanmfox](https://dev.to/seanmfox) 修复了一个问题，如果你属于一个组织并且没有为你的组织设置任何颜色，v1 编辑器将不能正确预览。解决方案是为组织设置默认颜色。谢谢， [@seanmfox](https://dev.to/seanmfox) ！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1764)

# 新的问题和讨论

*   [@kayis](https://dev.to/kayis) 报告了一个编辑器切换版本删除文字的 bug。不是一个有趣的错误。😞谢谢， [@kayis](https://dev.to/kayis) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 编辑切换版本并删除文字 #1733](https://github.com/thepracticaldev/dev.to/issues/1733) 

[![kay-is avatar](img/71341240750592ade64e56346c937786.png)](https://github.com/kay-is) **[kay-is](https://github.com/kay-is)** posted on [<time datetime="2019-02-04T16:01:31Z">Feb 04, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1733)

**描述 bug**

我写了一篇文章，按下预览，突然有了一个新版本的编辑器，但我的文章的上半部分不见了。

我的印象是这是一些用户界面故障，并按下保存草稿，但这只是坚持丢失的内容。

这不是一篇很长的文章，我只是重新写了一遍，但这是有点令人惊讶的行为。

**重现**

我无法重现这个问题，但当它发生时，我是这样做的:

1.  和老编辑一起写了一篇新文章
2.  新闻做了一些预览和保存
3.  按下预览，并得到一个新的编辑器与失踪的内容

**预期行为**

开发人员不应该在编辑过程中切换编辑器。

**桌面(请填写以下信息):**

*   操作系统:Windows 7
*   浏览器:火狐量子
*   版本:66.0b4 (64 位)

**智能手机(请填写以下信息):**

*   设备:[例如 iPhone6]
*   操作系统:[例如 iOS8.1]
*   浏览器[例如股票浏览器、safari]
*   版本[例如 22]

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1733)

*   [@vorillaz](https://dev.to/vorillaz) 请求了一个我们集成 web reference 的功能。更多细节请见本期。谢谢， [@vorillaz](https://dev.to/vorillaz) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加网页提及整合。 #1736](https://github.com/thepracticaldev/dev.to/issues/1736) 

[![vorillaz avatar](img/6802dc6369922fcc56c4aceb14426c40.png)](https://github.com/vorillaz) **[vorillaz](https://github.com/vorillaz)** posted on [<time datetime="2019-02-04T19:26:36Z">Feb 04, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1736)

你好，伟大的工作，我立刻爱上了这个社区和你的努力。作为 dev.to 的发布者，我希望我的内容能够得到反馈，因为它是交叉发布到 dev.to 的。

如果能整合[web reference . io](https://webmention.io/)就太好了。它只需要在 HTML 头上添加一个标签，并使用您的 twitter 帐户验证您的权威。提前感谢您的努力:)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1736)

*   @lightalloy 报告了一个不存在的播客片段出现在搜索结果中的错误。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 不存在的播客出现在搜索结果中 #1741](https://github.com/thepracticaldev/dev.to/issues/1741) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-02-05T11:49:45Z">Feb 05, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1741)

**描述 bug**[页面上的第一个播客](https://dev.to/search?q=books&filters=class_name:PodcastEpisode)返回 404。

**重现**重现行为的步骤:

1.  转到主页
2.  在搜索输入中键入“books”并提交
3.  在左侧菜单中选择“播客”。
4.  点击第一个结果(第 064 集-最喜欢的编程书籍)，查看“未找到”

**截图** [![book_podcasts](img/a6eb67dffcb55f9f56efafd4f0e822cc.png)](https://user-images.githubusercontent.com/30115/52271558-0b4c0080-2955-11e9-95d4-d1d22f33bc52.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1741)

*   [@jess](https://dev.to/jess) 发布了一个问题，作为完成评论模板功能的提醒。点击这里查看使用它的最新帖子:
    *   [链接到问题](https://github.com/thepracticaldev/dev.to/issues/1743)

[![jess](img/15e6599da1ea8b6f92e1b678e9347b2c.png)](/jess) [## 你这周赢了什么？

### 李佳薇 2 月 15 日 191 分钟阅读

#discuss #weeklyretro](/jess/what-was-your-win-this-week-50fe)

*   [@peter](https://dev.to/peter) 报告了一个错误，移动 Android 中的侧边栏可以滚动到最后一部分:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 覆盖主要进给环节【1746 号](https://github.com/thepracticaldev/dev.to/issues/1746)

[![pkfrank avatar](img/a6281578f8aad0af41d10413c0790fd2.png)](https://github.com/pkfrank) **[pkfrank](https://github.com/pkfrank)** posted on [<time datetime="2019-02-05T15:37:46Z">Feb 05, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1746)

这是一封发给`yo@`的电子邮件，提交者没有提供额外的信息，所以不幸的是我们对细节知之甚少。

**描述 bug** 看起来关键链接区域覆盖了主提要。

**重现**不明。这似乎是一个 Android 设备。如果其他人也遇到了这个问题，那将非常有帮助。

**截图** [![screenshot of issue on mobile](img/858c81cb744ccfb6be301d90c9e88a99.png)](https://camo.githubusercontent.com/3d4b5907a289d39ef6bf27ec51f27a9845dd6945/68747470733a2f2f636c2e6c792f3437623064646662343963382f496d616765253230323031392d30322d3035253230617425323031302e33322e3430253230414d2e706e67)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1746)

*   @sandordargo 报告了一个问题，他的帖子的浏览量没有变化。谢谢， [@sandordargo](https://dev.to/sandordargo) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 浏览量不变 #1765](https://github.com/thepracticaldev/dev.to/issues/1765) 

[![sandordargo avatar](img/a22f3605ce997a3ebfac00a6d8054c66.png)](https://github.com/sandordargo) **[sandordargo](https://github.com/sandordargo)** posted on [<time datetime="2019-02-08T10:32:57Z">Feb 08, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1765)

嗨，

我发现我的浏览量有些奇怪。

我上三篇帖子的浏览量(用户: [@sandordargo](https://dev.to/sandordargo) ，浏览量:156)都是一样的。

最后一条很奇怪，它已经有两天没变了，而反应的数量却在上升，而且它还出现在 twitter 上。我不认为在观看人数没有增加的情况下，反应的数量会增加。

谢谢你来看看。

干杯

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1765)

*   [@qm3ster](https://dev.to/qm3ster) 请求了一个不使用 Cloudinary 转换 SVG 的功能。目前正在讨论(由我发起)如何净化 SVG。感谢发行， [@qm3ster](https://dev.to/qm3ster) ！请在下面留下您的想法:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 不转换 SVG 与 Cloudinary  #1767](https://github.com/thepracticaldev/dev.to/issues/1767) 

[![qm3ster avatar](img/19f1b1a4e00be38b4617783066459eca.png)](https://github.com/qm3ster) **[qm3ster](https://github.com/qm3ster)** posted on [<time datetime="2019-02-08T19:16:41Z">Feb 08, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1767)

**您的功能请求是否与某个问题相关？请描述一下。**我今天用了一张矢量 svg 图片作为帖子的`cover_image`。它得到了 Cloudinary 的“优化”，并以自动格式(`f_auto`)送达。这不仅导致它变成了 6.2KB 的`webp`(甚至更大的`png`)，还导致[变得模糊](https://res.cloudinary.com/practicaldev/image/fetch/s--usNnnoz8--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://svgshare.com/i/B6d.svg)。网络上的[原始图像](https://svgshare.com/i/B6d.svg)为 781 字节。

**描述您想要的解决方案**如果可能，我们会提供经过消毒的 SVG。

**描述你考虑过的替代方案**我怀疑如果我自己把它转换成 png，它可能会不那么模糊，我以后可能会尝试。(同样使用白色背景而不是透明背景，因为这会导致开发人员使用灰色背景)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1767)

*   [@jeoxs](https://dev.to/jeoxs) 报告了一个问题，当侧边栏被打开时，整个页面滞后。查看正在讨论的问题。谢谢， [@jeoxs](https://dev.to/jeoxs) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 打开工具条时网站滞后(各一个) #1771](https://github.com/thepracticaldev/dev.to/issues/1771) 

[![Jeoxs avatar](img/89314376e9ec45b93970b8fbf794252e.png)](https://github.com/Jeoxs) **[Jeoxs](https://github.com/Jeoxs)** posted on [<time datetime="2019-02-09T22:07:32Z">Feb 09, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1771)

我最近写了一篇关于这个问题的文章，问人们是否有同样的问题:[https://dev.to/jeoxs/is-it-just-my-phone-or-devto-is-heavy-1h9l](https://dev.to/jeoxs/is-it-just-my-phone-or-devto-is-heavy--1h9l)

当我打开侧边栏时，网站开始冻结。我有一台三星 Galaxy S5 mini，安装了 Android 6.0.1 和最新的 Chrome 和 Android web view 版本。起初我以为是我的手机，但其他用户也在他们的设备上确认了这个问题。

提前感谢大家的支持:)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1771)

我想包括的最后一期是来自[周刊摘要机器人](https://github.com/apps/weekly-digest)的一期。在很大程度上，它有点挑剔，并不总是工作，但三周以来，它一直正常发布！这实际上产生了一个问题，即在我们的回购中发生的所有事情的清单:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 《每周文摘》(2019 年 2 月 4 日-2019 年 2 月 11 日) #1783](https://github.com/thepracticaldev/dev.to/issues/1783) 

[![weekly-digest[bot] avatar](img/5ea0dc2ffd717ac95a65152eae117f2f.png)](https://github.com/apps/weekly-digest) **[weekly-digest[bot]](https://github.com/apps/weekly-digest)** posted on [<time datetime="2019-02-11T11:10:40Z">Feb 11, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1783)

下面是 [*的**周刊摘要**实用 dev/dev.to*](https://github.com/thepracticaldev/dev.to) :

* * *

# 问题

上周发行了 53 期。其中，30 期已经结案，23 期仍未结案。

## 公开议题

<g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1773 [在底部内容缓存关键字](https://github.com/thepracticaldev/dev.to/pull/1773)中包含文章 ID，由[mstruve](https://github.com/mstruve)T7】💚 #1772 [当用户按下 ctrl+s](https://github.com/thepracticaldev/dev.to/issues/1772)时，通过[编辑](https://github.com/sarthology) <g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">保存帖子💚</g-emoji> #1771 [当我打开侧边栏(每一个)](https://github.com/thepracticaldev/dev.to/issues/1771)，被[jeo xs](https://github.com/Jeoxs)T19】💚 #1770 [多次点击发布会产生重复帖子](https://github.com/thepracticaldev/dev.to/issues/1770)， 由[who isryosuke](https://github.com/whoisryosuke)T25】💚 #1769 [标签模板并不总是起作用](https://github.com/thepracticaldev/dev.to/issues/1769)，由[Glenn men](https://github.com/Glennmen)T31】💚 #1767 [不转换带有云二进制](https://github.com/thepracticaldev/dev.to/issues/1767)的 SVG，由[qm3 ster](https://github.com/qm3ster)T37】💚 #1766 [[WIP]通过](https://github.com/thepracticaldev/dev.to/pull/1766)<g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">减少文章评论#1606 的 sql 查询次数💚</g-emoji> #1765 [视图数量不变](https://github.com/thepracticaldev/dev.to/issues/1765)， 由[sandordargo](https://github.com/sandordargo)T49】💚 #1762 [组织通知](https://github.com/thepracticaldev/dev.to/pull/1762)，由[赵](https://github.com/Zhao-Andy)安迪<g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1761 [增加了 `tag support to markdown editors in article posts and comm…`](https://github.com/thepracticaldev/dev.to/pull/1761) ，由[增加了](https://github.com/deadlybyte) <g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1760 [错误 Github PR 嵌入](https://github.com/thepracticaldev/dev.to/issues/1760)，由 [Glennmen](https://github.com/Glennmen) <g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji># 1758[https://dev.to/feed](https://github.com/thepracticaldev/dev.to/issues/1758)上的重复 RSS 条目，作者[IvanDelsinne](https://github.com/IvanDelsinne)T73】💚 #1748 [自动添加标签仲裁者到 mailchimp 列表](https://github.com/thepracticaldev/dev.to/issues/1748)， 由[jessleanyc](https://github.com/jessleenyc)T79】💚 #1746 [移动](https://github.com/thepracticaldev/dev.to/issues/1746)上覆盖主进给的关键环节，由 [pkfrank](https://github.com/pkfrank) <g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1745 [添加登陆页面](https://github.com/thepracticaldev/dev.to/issues/1745)的分割测试，由[benhalpern](https://github.com/benhalpern)T91】完成💚 #1744 [Bump jest 从 23.6.0 到 24.1.0](https://github.com/thepracticaldev/dev.to/pull/1744) ，由[dependent bot【bot】](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1743 [细化/敲定评论模板](https://github.com/thepracticaldev/dev.to/issues/1743)， 由[jessleanyc](https://github.com/jessleenyc)T103】💚 #1741 [搜索结果中出现不存在的播客](https://github.com/thepracticaldev/dev.to/issues/1741)，由[轻合金](https://github.com/lightalloy)T109】💚 #1739 [在标签页面](https://github.com/thepracticaldev/dev.to/pull/1739)上显示标签版主，由[seanmfox](https://github.com/seanmfox)T115】💚添加网络提及整合。 ，由[伏立兹](https://github.com/vorillaz) <g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1734 [不要用【输入】按钮](https://github.com/thepracticaldev/dev.to/issues/1734)在/内部/报告上发送电子邮件，由[jessleanyc](https://github.com/jessleenyc)<g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1733 [编辑切换了版本并删除了文本](https://github.com/thepracticaldev/dev.to/issues/1733)，由[凯-正](https://github.com/kay-is) <g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji> #1724 [撞帆从 1.5.1 到 2.1.1](https://github.com/thepracticaldev/dev.to/pull/1724) ，由[依赖机器人【bot】](https://github.com/apps/dependabot)

## 关闭议题

<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> #1768 [将 Bundler 更新到 2.0.1 & misc](https://github.com/thepracticaldev/dev.to/pull/1768) ，由[maestro MAC](https://github.com/maestromac)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1764[为组织设置默认颜色](https://github.com/thepracticaldev/dev.to/pull/1764)，由[seanmfox](https://github.com/seanmfox)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1763[将 html_variant 分割测试添加到/badge](https://github.com/thepracticaldev/dev.to/pull/1763) ，由 [benhalpern](https://github.com/benhalpern) 由[本哈彭](https://github.com/benhalpern)t30】❤️# 1756[检查用户是否存在于附加内容框中获取](https://github.com/thepracticaldev/dev.to/pull/1756)，由[本哈彭](https://github.com/benhalpern)t36】❤️# 1755】ping 评论来预热新的评论树，由[本哈彭](https://github.com/benhalpern) <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> #1754 [添加 profile_updated_at 和 git 由](https://github.com/thepracticaldev/dev.to/pull/1754)[PK frank](https://github.com/pkfrank)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1752[RSS feed 优化](https://github.com/thepracticaldev/dev.to/pull/1752)，由[benhalpern](https://github.com/benhalpern)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1751[用内容策略更新条款](https://github.com/thepracticaldev/dev.to/pull/1751)，由[PK frank](https://github.com/pkfrank)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1750[提高附加内容框和标签索引的性能【t66 关闭…](https://github.com/thepracticaldev/dev.to/pull/1750) ，由[aligoren](https://github.com/aligoren)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1747 增强内部索引页面，由[jesslenyc](https://github.com/jessleenyc)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1742[允许管理员查看仪表板并更新内部/用户权限](https://github.com/thepracticaldev/dev.to/pull/1742)，由[jesslenyc<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1740](https://github.com/jessleenyc) [by](https://github.com/jessleenyc) [押韵](https://github.com/rhymes) <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> #1737 [将特性测试改为系统测试](https://github.com/thepracticaldev/dev.to/pull/1737)，by[maestro MAC](https://github.com/maestromac)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1735[有时通过 RSS 从媒体导入带有 gist 的帖子不工作](https://github.com/thepracticaldev/dev.to/pull/1735)，by[jpac-run](https://github.com/jpac-run)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1732[增加了 3 周年纪念 由](https://github.com/thepracticaldev/dev.to/pull/1732)[link 2t twenty](https://github.com/Link2Twenty)t126】❤️# 1729[bump beautiful 从 1.16.1 到 1.16.4](https://github.com/thepracticaldev/dev.to/pull/1729) ，由[dependent bot【bot】](https://github.com/apps/dependabot)t132】❤️# 1728[bump eslint 从 5.12.1 到 5.13.0](https://github.com/thepracticaldev/dev.to/pull/1728) ，由[dependent bot【bot 由](https://github.com/apps/dependabot)[dependent bot【bot】](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1725[bump twilio-video 从 1.15.0 到 1.15.1](https://github.com/thepracticaldev/dev.to/pull/1725) ，由[dependent bot](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji># 1723[bump eslint-plugin-import 从 2.15.0 到 2.16.0](https://github.com/thepracticaldev/dev.to/pull/1723) ，由

## 蜃期

<g-emoji class="g-emoji" alias="+1" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44d.png">👍</g-emoji> #1739 [在标签页面](https://github.com/thepracticaldev/dev.to/pull/1739)显示标签版主，由 [seanmfox](https://github.com/seanmfox) 接收<g-emoji class="g-emoji" alias="+1" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44d.png">👍</g-emoji> x0，<g-emoji class="g-emoji" alias="smile" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f604.png">😄</g-emoji> x0，<g-emoji class="g-emoji" alias="tada" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f389.png">🎉</g-emoji> x1 和 <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> x0。

## 喧闹的问题

<g-emoji class="g-emoji" alias="speaker" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f508.png">🔈</g-emoji> #1737 [将特性测试改为系统测试](https://github.com/thepracticaldev/dev.to/pull/1737)，由 [maestromac](https://github.com/maestromac) 共收到 6 条评论。

* * *

# 拉请求

上周，创建、更新或合并了 45 个拉式请求。

## 开拉请求

上周，打开了 1 个拉取请求。<g-emoji class="g-emoji" alias="green_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49a.png">💚</g-emoji># 1766[【WIP】减少文章评论的 sql 查询数量#1606](https://github.com/thepracticaldev/dev.to/pull/1766) ，由[轻合金](https://github.com/lightalloy)

## 更新拉动请求

上周，更新了 12 个拉取请求。<g-emoji class="g-emoji" alias="yellow_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49b.png">💛</g-emoji> #1773 [在底部内容缓存关键字](https://github.com/thepracticaldev/dev.to/pull/1773)中包含文章 ID，由[mstruve](https://github.com/mstruve)T7】💛 #1762 [组织通知](https://github.com/thepracticaldev/dev.to/pull/1762)，由[赵](https://github.com/Zhao-Andy)安迪<g-emoji class="g-emoji" alias="yellow_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49b.png">💛</g-emoji> #1761 [增加 `tag support to markdown editors in article posts and comm…`](https://github.com/thepracticaldev/dev.to/pull/1761) ，由[增加](https://github.com/deadlybyte) <g-emoji class="g-emoji" alias="yellow_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49b.png">💛</g-emoji> #1744 [撞笑话从 23.6.0 到 24.1.0](https://github.com/thepracticaldev/dev.to/pull/1744) ，由[依赖机器人【bot】](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="yellow_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49b.png">💛</g-emoji> #1739 [在标签页面显示标签版主](https://github.com/thepracticaldev/dev.to/pull/1739)， 由[seanmfox](https://github.com/seanmfox)T31】💛 #1724 [由](https://github.com/thepracticaldev/dev.to/pull/1724)[【bot】](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="yellow_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49b.png">从 1.5.1 撞帆到 2.1.1 💛</g-emoji> #1682 [添加条纹取消网钩](https://github.com/thepracticaldev/dev.to/pull/1682)，由[jessleanyc](https://github.com/jessleenyc)T43】💛# 1633[【WIP】特征/自动保存编辑器](https://github.com/thepracticaldev/dev.to/pull/1633)，由 [aspittel](https://github.com/aspittel) <g-emoji class="g-emoji" alias="yellow_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49b.png">💛</g-emoji> #1579 [添加点击复制到文章分享下拉列表](https://github.com/thepracticaldev/dev.to/pull/1579)，由[benny powers](https://github.com/bennypowers)T55】💛# 1563[【WIP】连接打开的收件箱](https://github.com/thepracticaldev/dev.to/pull/1563)， 由[marioco 参见](https://github.com/mariocsee)T61】💛 #1524 [用于文章分享](https://github.com/thepracticaldev/dev.to/pull/1524)，作者<g-emoji class="g-emoji" alias="yellow_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49b.png">💛</g-emoji> #1408 [[WIP]通过](https://github.com/thepracticaldev/dev.to/pull/1408)[押韵](https://github.com/rhymes)将 Rails 升级到 5.2.2

## 合并拉取请求

上周，32 个拉取请求被合并。<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1764 [通过](https://github.com/thepracticaldev/dev.to/pull/1764) [seanmfox](https://github.com/seanmfox) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">为组织设置默认颜色💜</g-emoji> #1763 [添加 html_variant 分割测试到/badge](https://github.com/thepracticaldev/dev.to/pull/1763) ，由[benhalpern](https://github.com/benhalpern)T12】💜 #1759 [使 Spotify 和 generic Liquid 标签更加严格](https://github.com/thepracticaldev/dev.to/pull/1759)，由[赵-安迪](https://github.com/Zhao-Andy) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1757 [修复配置文件缓存泄漏问题](https://github.com/thepracticaldev/dev.to/pull/1757)， 由[本哈尔彭](https://github.com/benhalpern)T24】💜 #1756 [检查附加内容框中是否存在用户获取](https://github.com/thepracticaldev/dev.to/pull/1756)，由[benhalpern](https://github.com/benhalpern)T30】💜 #1755 [Ping 评论预热新评论树](https://github.com/thepracticaldev/dev.to/pull/1755)，由[benhalpern](https://github.com/benhalpern)T36】💜 #1754 [添加 profile_updated_at 和 github_repos_updated_at 以避免额外的 fet…](https://github.com/thepracticaldev/dev.to/pull/1754) ，作者 [benhalpern](https://github.com/benhalpern) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1753 [更新数字海洋链接/标语](https://github.com/thepracticaldev/dev.to/pull/1753)， 由[PK frank](https://github.com/pkfrank)T48】💜 #1752 [RSS feed 优化](https://github.com/thepracticaldev/dev.to/pull/1752)，由[benhalpern](https://github.com/benhalpern)T54】💜 #1751 [用内容策略](https://github.com/thepracticaldev/dev.to/pull/1751)更新条款，由 [pkfrank](https://github.com/pkfrank) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1750 [提高附加内容框和标签索引](https://github.com/thepracticaldev/dev.to/pull/1750)的性能，由 [benhalpern](https://github.com/benhalpern) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1749 [修正了“你自己帖子上编辑帖子按钮的样式”的问题。 关闭…](https://github.com/thepracticaldev/dev.to/pull/1749) ，由[对齐](https://github.com/aligoren)至<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1747 [增强内部索引页面](https://github.com/thepracticaldev/dev.to/pull/1747)，由[jessleanyc](https://github.com/jessleenyc)T78】提供💜 #1742 [允许管理员查看仪表板并更新内部/用户](https://github.com/thepracticaldev/dev.to/pull/1742)中的权限，由[jessleanyc](https://github.com/jessleenyc)<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1738 [给徽章图像添加标题](https://github.com/thepracticaldev/dev.to/pull/1738)，由[押韵](https://github.com/rhymes) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1735 [有时通过 RSS 从媒体导入带有要点的帖子不起作用](https://github.com/thepracticaldev/dev.to/pull/1735)， 由 [jpac-run](https://github.com/jpac-run) 运行<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1732 [追加 3 周年徽章](https://github.com/thepracticaldev/dev.to/pull/1732)，由[格伦门](https://github.com/Glennmen)T102】💜 #1731 [附加-主题-容器-颜色](https://github.com/thepracticaldev/dev.to/pull/1731)，由[link 2 twenty](https://github.com/Link2Twenty)T108】💜 #1729 [从 1.16.1 到 1.16.4](https://github.com/thepracticaldev/dev.to/pull/1729) ，由[依赖机器人【机器人】](https://github.com/apps/dependabot) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1728 [将 eslint 从 5.12.1 提升到 5.13.0](https://github.com/thepracticaldev/dev.to/pull/1728) ，由[dependent bot【bot】](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1727 [将 eslint-plugin-jsx-a11y 从 6.2.0 提升到 6.2.1](https://github.com/thepracticaldev/dev.to/pull/1727) ， 由[依赖机器人](https://github.com/apps/dependabot)依赖机器人<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji># 1725[Bump twilio-video 从 1.15.0 到 1.15.1](https://github.com/thepracticaldev/dev.to/pull/1725) ，由[dependent bot【bot】](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1723 [将 eslint-plugin-import 从 2.15.0 升级到 2.16.0](https://github.com/thepracticaldev/dev.to/pull/1723) ，由[dependent bot【bot】](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1722 [凸起棉绒-从 8.1.0 升级到 8.1.3](https://github.com/thepracticaldev/dev.to/pull/1722) ，由[依赖机器人【机器人】](https://github.com/apps/dependabot) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">执行💜</g-emoji> #1721 [颠簸云从 1.9.1 到 1.11.1](https://github.com/thepracticaldev/dev.to/pull/1721) ， 由[依赖机器人](https://github.com/apps/dependabot)依赖机器人<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1720 [从 2.27.0 到 2.27.1 的碰撞平行 _ 测试](https://github.com/thepracticaldev/dev.to/pull/1720)，由[依赖机器人【机器人】](https://github.com/apps/dependabot) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1713 [更新 Rubocop 配置以针对 Ruby 2.6 并使用安全自动更正](https://github.com/thepracticaldev/dev.to/pull/1713)，通过[押韵](https://github.com/rhymes) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1712 [删除重复的哈士奇配置](https://github.com/thepracticaldev/dev.to/pull/1712)，由[押韵](https://github.com/rhymes) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜将 Ruby 升级到 2.6.1 版本， 由[押韵](https://github.com/rhymes)到<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1699 [通过](https://github.com/thepracticaldev/dev.to/pull/1699)[轻合金](https://github.com/lightalloy) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">修复在物品销毁#1621 的延迟::作业中找到的 active record::record not💜</g-emoji> #1693 [改进规格](https://github.com/thepracticaldev/dev.to/pull/1693)，由 [maestromac](https://github.com/maestromac) <g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji> #1680 [更新天光徽章指向正确的 URL【跳过 ci】](https://github.com/thepracticaldev/dev.to/pull/1680)，由 [vaidehijoshi](https://github.com/vaidehijoshi)</g-emoji>

* * *

# 提交

上周有 32 次提交。 <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> [设置组织默认颜色(# 1764)](https://github.com/thepracticaldev/dev.to/commit/693215bb084445d2b4ac8d171e3e44badd4d2edf)by[seanmfox](https://github.com/seanmfox)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【将 html_variant 拆分测试添加到/badge(# 1763)* add/badge html _ variant

*   修复了由[本哈尔彭](https://github.com/benhalpern) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> 【修复 active record::record not found in delayed::jobs on article destroy(# 1699)*修复了文章的`ActiveRecord::NotFound`问题

*   从文章回调中提取了 ActiveJobs

*   添加了文章和反应删除的规范]([https://github . com/thepractical dev/dev . to/commit/7f 6 E0 cf 185 a2 C5 1c C2 C1 d 3198 d0f 9 c 57 a 5602 db 3](https://github.com/thepracticaldev/dev.to/commit/7f6e0cf185a2c51cc2c1d3198d0f9c57a5602db3))作者[light alloy](https://github.com/lightalloy)T4【🛠️】t5【使 Spotify 和通用液体标签更加严格(#1759) *再添加几个液体标签

*   更新 regex 更严格]([https://github . com/thepractical dev/dev . to/commit/EEC 3 FB 87902 DD 93765 a9 c 81 AFA 157 e 71649601 C2](https://github.com/thepracticaldev/dev.to/commit/eec3fb87902dd93765a9c81afa157e71649601c2))由[赵-安迪](https://github.com/Zhao-Andy) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> [修复配置文件缓存问题(#1757)](https://github.com/thepracticaldev/dev.to/commit/7e9b7f2d8ed33a21f997448206b8a535b9369492) 由[本哈尔彭](https://github.com/benhalpern) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> [Ping 评论到](https://github.com/thepracticaldev/dev.to/commit/4bff494adce50c38aa87b2c124130a109e667218)

*   修复变量编号问题

*   修复测试]([https://github . com/thepractical dev/dev . to/commit/71a 419 e 5 eeff 478855 b 9 df 12a 12448 f 589d 00 D8 b](https://github.com/thepracticaldev/dev.to/commit/71a419e5eeff478855b9df12a12448f589d00d8b))作者[本哈尔彭](https://github.com/benhalpern)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【RSS 订阅源优化(#1752) * RSS 订阅源优化

*   更新应用程序/视图/文章/feed.rss.builder

合著者:本哈尔彭[bendhalpern@gmail.com](mailto:bendhalpern@gmail.com)

*   更新 app/controllers/articles _ controller . Rb

*   更新 app/controllers/articles _ controller . Rb]([https://github . com/thepractical dev/dev . to/commit/7 d6c 85 a 29490 cab 49558 ea 03 a 5669 EEA 2 f 0f 458 c](https://github.com/thepracticaldev/dev.to/commit/7d6c85a29490cab49558ea03a5669eea2f0f458c))由 [benhalpern](https://github.com/benhalpern) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> 【更新数字海洋(#1753) -更新数字海洋标语/链接

*   在主页和赞助商页面]([https://github . com/the practical dev/dev . to/commit/9 c 0 bfd 6 CD 06 baec 76462 DDC 951563 f 036 Fe 60337](https://github.com/thepracticaldev/dev.to/commit/9c0bfd6cd06baec76462ddc951563f036fe60337))由 [pkfrank](https://github.com/pkfrank) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> [更新条款与内容政策(#1751) -新增“内容政策”语言](https://github.com/thepracticaldev/dev.to/commit/da968cef43ce99d1884453d970e52d08e2997722)由 [pkfrank](https://github.com/pkfrank) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> [更新这个提交更新了指向公共仪表板的 url，这样所有 dev.to 贡献者都可以查看开源性能数据。](https://github.com/thepracticaldev/dev.to/commit/f23ead7a942f0f9c0df059b0f00badba7de7e0d4)by[vaidehijoshi](https://github.com/vaidehijoshi)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【提高附加内容框和标签索引的性能(#1750) *提高附加内容框和标签索引的性能

*   用新的经典文章 api 修改 followed_articles 端点]([https://github . com/thepractical dev/dev . to/commit/2d 97 a5d 83749861 a 2c 36e 73819d 048104 F8 dda6c](https://github.com/thepracticaldev/dev.to/commit/2d97a5d83749861a2c36e73819d048104f8dda6c))by[benhalpern](https://github.com/benhalpern)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【将 Ruby 升级到 2.6.1 (#1711) *将 Ruby 升级到 2.6.1

Ruby 2.6.1 包含对 Net::HTTP 的修复

*   将 2.6.1 添加到。也是 ruby 版本

*   将 2.6.1 也添加到 Travis ci]([https://github . com/the practical dev/dev . to/commit/DD 6372012 def 4c 0a 645711 b 6916 e 7 DDE 020 df 255](https://github.com/thepracticaldev/dev.to/commit/dd6372012def4c0a645711b6916e7dde020df255))由[押韵](https://github.com/rhymes) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> 【问题“编辑你自己帖子上的帖子按钮的样式”已修复。关闭… (#1749) *问题“你自己的帖子上的编辑帖子按钮的样式”已修复。关闭#1740

*   我将 margin top 更改为 4，媒体屏幕的最大宽度值更改为 376 px]([https://github . com/the practical dev/dev . to/commit/b 9279033 a4 ace 3 a 7 f 4d 459 CDC 033 c 6 F5 bfd 1 AFE 0](https://github.com/thepracticaldev/dev.to/commit/b9279033a4ace3a7f4d459cdc033c6f5bfd1afe0))由 [aligoren](https://github.com/aligoren) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> 【允许管理员查看仪表板并更新内部/用户(#1742)中的权限)*允许管理员查看仪表板

*   向内部用户添加更多信息

*   向内部用户添加更多信息

*   添加视频权限开关

*   指定被驱逐的用户特征]([https://github . com/thepractical dev/dev . to/commit/009001272 a 63 cf 4c E7 aade 59491 a 166 a9 A8 d 1382](https://github.com/thepracticaldev/dev.to/commit/009001272a63cf4ce7aade59491a166a9a8d1382))由[jessleanyc](https://github.com/jessleenyc)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【增加 3 周年纪念徽章(#1732) *增加 3 周年纪念徽章

*   修复林挺错误]([https://github . com/thepractical dev/dev . to/commit/1793956 BD B1 c 0 C2 aa 8261 b 6070 a9fd 5381 e 12409](https://github.com/thepracticaldev/dev.to/commit/1793956bdb1c0c2aa8261b6070a9fd5381e12409))作者[格伦门](https://github.com/Glennmen)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>[增强内部索引页面(#1747) *向内部用户索引添加更多信息

*   创建标签显示页面并添加标签审核表单

*   修复林挺

*   添加标签索引视图和别名字段

*   向标签索引添加分页

*   fix spec]([https://github . com/thepractical dev/dev . to/commit/54426 ea 20 b 29314320 eee 631 a 6000 cb4 e 413 ce0a](https://github.com/thepracticaldev/dev.to/commit/54426ea20b29314320eee631a6000cb4e413ce0a))by[jesslenyc](https://github.com/jessleenyc)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>[删除重复的 husky 配置(#1712)](https://github.com/thepracticaldev/dev.to/commit/fe9da91c57c49fb3ddc0d904659c82943e9ec45f) by [押韵](https://github.com/rhymes) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> [添加标题到徽章图片(# 1733](https://github.com/thepracticaldev/dev.to/commit/ecad47eb3c9f97170487d2721039716bb7af6856)

*   [发行说明](https://github.com/cloudinary/cloudinary_gem/releases)
*   [变更日志](https://github.com/cloudinary/cloudinary_gem/blob/master/CHANGELOG.md)
*   [提交](https://github.com/cloudinary/cloudinary_gem/compare/1.9.1...1.11.1)

落款:dependent bot[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/a3 bb 0b 831 a5 ef 1ee 762 FDC 0a 2 fc 96 a 590379 cc 71](https://github.com/thepracticaldev/dev.to/commit/a3bb0b831a5ef1ee762fdc0a2fc96a590379cc71))作者:[dependent bot[bot]](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【bump twilio-video 从 1.15.0 到 1 . 15 . 1(# 1725)【ci skip】bumps

*   [发行说明](https://github.com/twilio/twilio-video.js/releases)
*   [变更日志](https://github.com/twilio/twilio-video.js/blob/1.15.1/CHANGELOG.md)
*   [提交](https://github.com/twilio/twilio-video.js/compare/1.15.0...1.15.1)

落款:依赖机器人[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/0090 ee 0 dbb 5b 9 de 618 AFD 732 dff 09 e 983 e 534052](https://github.com/thepracticaldev/dev.to/commit/0090ee0dbb5b9de618afd732dff09e983e534052))作者:[依赖机器人[bot]](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【bump lint-staged 从 8.1.0 到 8 . 1 . 3(# 1722)【ci skip】bumps【t8t

*   [发行说明](https://github.com/okonet/lint-staged/releases)
*   [提交](https://github.com/okonet/lint-staged/compare/v8.1.0...v8.1.3)

落款:依存机器人[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/BF 3823 e 1645 e 8d 4 c 17 b5 b5 f 857 ecee 1218 f 8d 3 a 1](https://github.com/thepracticaldev/dev.to/commit/bf3823e1645e8d4c17b5b5f857ecee1218f8d3a1))作者:[依存机器人[bot]](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【bump parallel _ tests 从 2.27.0 到 2 . 27 . 1(# 1720)【ci skip】bumps

*   [发行说明](https://github.com/grosser/parallel_tests/releases)
*   [提交](https://github.com/grosser/parallel_tests/compare/v2.27.0...v2.27.1)

落款:依赖机器人[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/429d 35 B9 a 16492 B2 ee 42809 CB 54 f 72 ded 4464d 56](https://github.com/thepracticaldev/dev.to/commit/429d35b9a16492b2ee42809cb54f72ded4464d56))作者:[依赖机器人[bot]](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【bump eslint-plugin-import 从 2.15.0 升级到 2.16.0 (#1723)

*   [发行说明](https://github.com/benmosher/eslint-plugin-import/releases)
*   [变更日志](https://github.com/benmosher/eslint-plugin-import/blob/master/CHANGELOG.md)
*   [提交](https://github.com/benmosher/eslint-plugin-import/compare/v2.15.0...v2.16.0)

落款人:从属机器人[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/77 d66 f 5423821 D1 f 08 f 1505861374d 84 e 03 f 78 b 9](https://github.com/thepracticaldev/dev.to/commit/77d66f5423821d1f08f1505861374d84e03f78b9))由[从属机器人[bot]](https://github.com/apps/dependabot) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> 【将 eslint 从 5.12.1 提升到 5 . 13 . 0(# 1728)【ci

*   [发行说明](https://github.com/eslint/eslint/releases)
*   [变更日志](https://github.com/eslint/eslint/blob/master/CHANGELOG.md)
*   [提交](https://github.com/eslint/eslint/compare/v5.12.1...v5.13.0)

落款:dependent bot[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/5 F2 bafb 8 F5 b 1046 ec9 D1 d 6788 EB 2227064 dfbe 7d](https://github.com/thepracticaldev/dev.to/commit/5f2bafb8f5b1046ec9d1d6788eb2227064dfbe7d))由[dependent bot[bot]](https://github.com/apps/dependabot)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>[更新了 gist regex 以匹配来自媒体的 URL。(# 1735)](https://github.com/thepracticaldev/dev.to/commit/0888b9df1fe596b4dacac61623a6b9d70e716b8f)by[jpac-run](https://github.com/jpac-run)<g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji>【改进规格(#1693) * Lock erb_lint

*   重构审核规范

*   移除并替换 RequestSpecHelper

*   将 uid 更新为序列

*   降低规范中对持久性的需求

*   更新审核规范

*   更新 shoulda-matchers

*   启用后台作业并更新注释规范

*   修复失败规格

*   更新注释的验证

*   删除多余的规格

*   真的关掉 DJ

*   立即应用运行后台作业

*   移除免费规格

*   创建身份规范】([https://github . com/thepractical dev/dev . to/commit/1137 D6 FBA 40893156 a 06 c 20 c 84 D6 D5 CFA 46 e 8246](https://github.com/thepracticaldev/dev.to/commit/1137d6fba40893156a06c20c84d6d5cfa46e8246))由 [maestromac](https://github.com/maestromac) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> 【凹凸 eslint-plugin-jsx-a11y 从 6.2.0 到 6.2.1 (#1727)凹凸 [eslint-plugin-jsx-a11y](https://github.com/evcohen/eslint-plugin-jsx-a11y)

*   [发行说明](https://github.com/evcohen/eslint-plugin-jsx-a11y/releases)
*   [变更日志](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/CHANGELOG.md)
*   [提交](https://github.com/evcohen/eslint-plugin-jsx-a11y/compare/v6.2.0...v6.2.1)

落款:依赖机器人[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/aa 16 CAD 4297 a 8281 f 26 f 44893841 aaadbbb 80209](https://github.com/thepracticaldev/dev.to/commit/aa16cad4297a8281f26f44893841aaadbbb80209))作者:[依赖机器人[bot]](https://github.com/apps/dependabot) <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> 【附加-主题-容器-颜色(#1731) *附加-主题-容器-颜色

初次传球失误

*   附加主题容器颜色

初次传球失误

*   附加主题容器颜色

初次传球失误

*   附加主题容器颜色

初次过关时错过】([https://github . com/thepractical dev/dev . to/commit/9 E4 d 9 ecac 16 a 70 CBE 64 c 351506 cecf 3d e 5 e 12459](https://github.com/thepracticaldev/dev.to/commit/9e4d9ecac16a70cbe64c351506cecf3de5e12459))由[link 2t wenty](https://github.com/Link2Twenty)T4【凹凸更漂亮从 1.16.1 到 1.16.4(# 1729)【ci skip】凹凸[凹凸更漂亮](https://github.com/prettier/prettier)从 1.16.1 到 1 . 16 . 4

*   [发行说明](https://github.com/prettier/prettier/releases)
*   [变更日志](https://github.com/prettier/prettier/blob/master/CHANGELOG.md)
*   [提交](https://github.com/prettier/prettier/compare/1.16.1...1.16.4)

落款:dependent bot[bot][support@dependabot.com](mailto:support@dependabot.com)([https://github . com/thepractical dev/dev . to/commit/ed 03 C4 cf 0f 0 BAC 2 BC 56556 ff 606 f 8 c8 c 0 ba 18](https://github.com/thepracticaldev/dev.to/commit/ed03c4cf0cf6f0bac2bc56556ff606f8c8c0ba18))作者:[dependent bot[bot]](https://github.com/apps/dependabot)

* * *

# 投稿人

上周有 14 个贡献者。<g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [seanmfox](https://github.com/seanmfox) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [本哈尔彭](https://github.com/benhalpern) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [轻合金](https://github.com/lightalloy) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [赵——安迪](https://github.com/Zhao-Andy) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji>T18】PK frankT20】👤 [押韵](https://github.com/rhymes) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [vaidehijoshi](https://github.com/vaidehijoshi) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [阿利戈伦](https://github.com/aligoren) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji>[jessleanyc](https://github.com/jessleenyc)<g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [格伦门](https://github.com/Glennmen) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [【塌实机器人】](https://github.com/apps/dependabot) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [jpac-run](https://github.com/jpac-run) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [maestromac](https://github.com/maestromac) <g-emoji class="g-emoji" alias="bust_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f464.png">👤</g-emoji> [链接 2 第二十](https://github.com/Link2Twenty)

* * *

# 观星者

上周有 68 名实习生。<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【cnminh】](https://github.com/cnminh)<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【ram 231】](https://github.com/ram231)<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【hos】](https://github.com/hosos) <g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png"><g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【数码漂移机】](https://github.com/digital-drifter)<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【严桂林】 <g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png"><g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">【phineazycc】</g-emoji></g-emoji>](https://github.com/yan-guilherme)【T1112】</g-emoji>【T1114】【leznov】<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">【T1117】【T1118】【T1119】【t】 [丹尼巴莱【T1163】](https://github.com/danielbayley)<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【奥马尔 25h】](https://github.com/omar25h)</g-emoji> <g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png"><g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【http 请求】](https://github.com/http-request)<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">[【yovanycunh】](https://github.com/yovanycunha)</g-emoji>[【jeremydy】](https://github.com/jeremykenedy) <g-emoji class="g-emoji" alias="star2" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f31f.png">🌟</g-emoji></g-emoji></g-emoji></g-emoji></g-emoji></g-emoji></g-emoji></g-emoji></g-emoji>

* * *

# 发布

上周没有发布。

* * *

上周到此为止，请`<g-emoji class="g-emoji" alias="eyes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f440.png">👀</g-emoji> **Watch**`和`<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">⭐</g-emoji> **Star**`资源库[T3】the practical dev/dev . toT5】接收下周更新。<g-emoji class="g-emoji" alias="smiley" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f603.png">😃</g-emoji>](https://github.com/thepracticaldev/dev.to)

*你也可以[点击这里](https://github.com/thepracticaldev/dev.to/issues?q=is:open+is:issue+label:weekly-digest)查看所有的每周摘要。*

> 你的 [**周刊文摘**](https://github.com/apps/weekly-digest) bot。<g-emoji class="g-emoji" alias="calendar" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4c6.png">📆</g-emoji>

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1783)

这星期到此为止！我们看看下周是否还有每周文摘摘要...