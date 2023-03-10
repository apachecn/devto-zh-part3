# 开发人员对过去两周的回购进行总结

> 原文：<https://dev.to/devteam/devto-repo-recap-from-the-past-two-weeks-10ok>

欢迎回到另一个回购概述。通常，我们涵盖了上周对 dev.to 库的贡献(T1)和对 iOS 回购(T3)的贡献(T2)。本周，我们将报道 1 月 20 日至 2 月 2 日。

# 特性

*   感谢[@韵脚](https://dev.to/rhymes)在你的仪表板中重新排列了[关注的标签页面，使其按照权重、受欢迎程度和名字的升序排序。](https://dev.to/dashboard/following)

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1583)
*   多亏了 [@kyleboe](https://dev.to/kyleboe) ，你现在可以将 Spotify 嵌入到你的帖子和评论中了！

    *   [链接到公关](https://github.com/thepracticaldev/dev.to/pull/1573)这里有一个例子:

```
{% spotify spotify:episode:5V4XZWqZQJvbddd31n56mf %} 
```

Enter fullscreen mode Exit fullscreen mode

[https://open.spotify.com/embed/episode/5V4XZWqZQJvbddd31n56mf](https://open.spotify.com/embed/episode/5V4XZWqZQJvbddd31n56mf)

*   感谢 [@davefollett](https://dev.to/davefollett) 为 CodeSandbox 嵌入添加了`initialpath`和`module`选项！更多详情请查看公关。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1545)
*   您现在可以为您自己的文章和您评论过的评论主题设置静音通知！更新日志和 PR 中的更多详细信息:

    *   [商品 PR 的静音通知](https://github.com/thepracticaldev/dev.to/pull/1594)
    *   [静音通知评论公关](https://github.com/thepracticaldev/dev.to/pull/1636)

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/2c8741e5fbb812e2e857e3cd00f499f7.png)](/devteam) [## 新功能:静音通知

### 开发团队的 Andy Zhao(他/他)1 月 24 日 1911 分钟阅读

#changelog #meta](/devteam/new-feature-mute-notifications-70m)

*   [@ben](https://dev.to/ben) 增加了书写条纹徽章，还有算法自动奖励你！查看下面的 PR 和 changelog 帖子。徽章感谢 [@liana](https://dev.to/liana) ！
    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1670)

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## 新徽章即将推出:连续 4 周

### 开发团队的 Ben Hal pern 1919 年 1 月 4 日 1 分钟阅读

#meta #rails #contributorswanted #writing](/devteam/new-badge-coming-soon-4-week-streak-26db)

*   感谢 [@link2twenty](https://dev.to/link2twenty) 重构了 StackBlitz Liquid 标签，并增加了选择默认视图的功能！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1649)
*   感谢 [@juanmanuelramallo](https://dev.to/juanmanuelramallo) 在 Markdown 中增加了转换`:colon:`表情符号的功能！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1653)
*   我们为 post 发布了评论模板的(封闭)测试版！这将允许您有一个预填充评论模板的帖子供人们使用。感谢 [@aspittel](https://dev.to/aspittel) 和 [@ben](https://dev.to/ben) 把它弄出来！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1692)
*   感谢[@韵](https://dev.to/rhymes)激活`eslint-plugin-jsx-a11y`！这将为我们即时解决所有问题。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1688)

# Bug 修复/其他贡献

*   [@link2twenty](https://dev.to/link2twenty) 继续为黑暗主题添加 CSS 变量的工作:

    *   [添加主题容器颜色](https://github.com/thepracticaldev/dev.to/pull/1706)
*   感谢 [@lightalloy](https://dev.to/lightalloy) 对`stories view`的重构，这是我们处理大多数页面的主要方式。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1584)
*   @ben 对我们的缓存逻辑做了一些改进。如果您对我们如何提高绩效感兴趣，请查看下面的 PRs:

    *   [修改命中率低的缓存键](https://github.com/thepracticaldev/dev.to/pull/1596)
    *   [缓存集合，因此不必重新获取](https://github.com/thepracticaldev/dev.to/pull/1576)
    *   [删除文章收藏区缓存](https://github.com/thepracticaldev/dev.to/pull/1597)
    *   [增加一些缓存优化和查询保护](https://github.com/thepracticaldev/dev.to/pull/1598)
    *   [修改侧贴和评论. rb 缓存/触摸](https://github.com/thepracticaldev/dev.to/pull/1599)
    *   [添加额外的主页缓存](https://github.com/thepracticaldev/dev.to/pull/1610)
    *   [修改侧边栏缓存](https://github.com/thepracticaldev/dev.to/pull/1675)
    *   [修改流量大的视图的缓存键](https://github.com/thepracticaldev/dev.to/pull/1681)
    *   [修改 article-sticky-nav-articles 的缓存键](https://github.com/thepracticaldev/dev.to/pull/1683)
    *   [修改首页缓存](https://github.com/thepracticaldev/dev.to/pull/1695)
*   [@kenbellows](https://dev.to/kenbellows) 修复了 CodePen Liquid 标签默认标签解析不正确的问题。谢了，肯！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1609)
*   [@archiet](https://dev.to/archiet) 在允许列表中增加了两个乳齿象实例。谢谢@archiet！

    *   [添加“BSD . network”](https://github.com/thepracticaldev/dev.to/pull/1611)
    *   [添加“101010 . pl”](https://github.com/thepracticaldev/dev.to/pull/1612)
*   [@aspittel](https://dev.to/aspittel) 修复了在 v2 编辑器的标签栏中输入的 bug。谢谢你，阿里！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1614)
*   [@mariocsee](https://dev.to/mariocsee) 修复了大屏幕的一个小 CSS 问题。谢了。马里奥！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1523)
*   升级 Rubocop，我们的 Ruby linter 和风格指南宝石，并修正了一些问题。感谢 [@vinistock](https://dev.to/vinistock) ！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1635)
*   [@link2twenty](https://dev.to/link2twenty) 重构了`initializeUserSidebar`文件。谢谢安德鲁。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1640)
*   @mariocsee 修复了 Connect 中容器宽度跳跃的问题。谢谢，马里奥！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1652)
*   [@lightalloy](https://dev.to/lightalloy) 也在我们的预提交钩子中添加了`erb_lint`来检查`.html.erb`(嵌入 Ruby 的 HTML)模板文件。谢谢安娜。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1657)
*   代码跨度现在工作正常！您应该能够使用双反勾号来呈现单个反勾号代码示例。例如:`a JS template literal: `cool ${beanFlavor}``

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1678)
*   [@gregbrimble](https://dev.to/gregbrimble) 更正了配置文件字段的前端验证，以匹配我们的后端最大长度验证。谢谢格雷格。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1691)
*   [@韵脚](https://dev.to/rhymes)修复了评论模板中臭名昭著的`undefined`会出现的一个小错误。谢谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1700)
*   [@nickytonline](https://dev.to/nickytonline) 升级了我们的哈士奇配置。谢谢尼克。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1686)
*   我们已经更新了我们的问题级别，使其更加直观。这主要是内部词汇，但请随意查看:

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1690)
*   [@seanmfox](https://dev.to/seanmfox) 修正了‘为开发环境获取 API 密钥’文档中的一些语法和标点符号。谢谢肖恩。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1708)
*   为 i18n 回退添加了默认区域设置。谢谢，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1714)
*   [@aligoren](https://dev.to/aligoren) 修复了一个人的个人资料中长行不包裹 bug 的问题。谢谢@阿里戈伦！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1715)
*   [@jess](https://dev.to/jess) 更新了一堆我们内部的工装。谢谢杰西。

    *   [修复 Banisher 更新](https://github.com/thepracticaldev/dev.to/pull/1704)
    *   [添加内部/文章输入以更新作者](https://github.com/thepracticaldev/dev.to/pull/1628)
    *   [删除用户功能](https://github.com/thepracticaldev/dev.to/pull/1615)
    *   [取消订阅 Mailchimp 简讯的已删除/已驱逐用户](https://github.com/thepracticaldev/dev.to/pull/1644)
    *   [修正条件，以便超级管理员可以禁止旧的垃圾邮件发送者](https://github.com/thepracticaldev/dev.to/pull/1651)
    *   [修复/管理标签版主更新](https://github.com/thepracticaldev/dev.to/pull/1676)
    *   [放逐时清除档案缓存](https://github.com/thepracticaldev/dev.to/pull/1702)

# 新的问题和讨论

*   [@prathaprathod](https://dev.to/prathaprathod) 请求通过 OAuth 登录 Google 的功能。谢谢@parthaprathod！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加谷歌登录 #1595](https://github.com/thepracticaldev/dev.to/issues/1595) 

[![prathaprathod avatar](img/49899af8a347c4bc4acddd8790279d21.png)](https://github.com/prathaprathod) **[prathaprathod](https://github.com/prathaprathod)** posted on [<time datetime="2019-01-20T04:06:52Z">Jan 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1595)

嗨，开发团队

我正在使用 dev.to 社区，这个网站是一流的，我需要功能添加谷歌认证的人谁没有 twitter 或 github 这些人通过谷歌登录或添加电子邮件和密码

希望这个功能会考虑

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1595)

*   [@ben](https://dev.to/ben) 提出了减少文章展示页面评论查询次数的问题。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 减少对文章展示页面评论的查询。 #1606](https://github.com/thepracticaldev/dev.to/issues/1606) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-01-21T15:43:19Z">Jan 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1606)

**描述 bug** 我们在文章展示页面上的最坏情况是一个 n+1 问题。我们做了大量的缓存，我们有一个*有点*有效的方法来避免*字面上的*每个查询。但是，我们仍然对一些请求进行额外的查询，当一个有很多评论的页面被冷加载时，它可能是一个很大的性能异常。

**复制**在开发中创建一篇文章，并添加一堆不同级别的评论。您应该会看到许多查询发生。

**预期行为**单个查询应该足以将所有评论加载到内存中。属于一篇文章的所有评论都有该文章的`commentable_id`,甚至是子评论。我们使用[血统宝石](https://github.com/stefankroes/ancestry)来处理树逻辑。

因为我们可以通过一个查询获取所有可能成为树的一部分的评论，所以我们*应该*能够只进行一个评论查询，然后从那里开始处理那个集合。

我认为解决方案应该是这样的:

[https://stack overflow . com/questions/32207398/how-to-perform-eager-loading-on-model-descendants-using-rails-and-ancestry](https://stackoverflow.com/questions/32207398/how-to-perform-eager-loading-on-model-descendants-using-rails-and-ancestry)

谢谢！

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1606)

*   [@zcdunn](https://dev.to/zcdunn) 引发了乳齿象标签是否正确的讨论。请随意插话:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 档案字段  #1607](https://github.com/thepracticaldev/dev.to/issues/1607) 标签不正确

[![zcdunn avatar](img/30d34f43306d72e9292cb094ffb36a6f.png)](https://github.com/zcdunn) **[zcdunn](https://github.com/zcdunn)** posted on [<time datetime="2019-01-21T16:14:28Z">Jan 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1607)

**描述错误**个人资料字段“乳齿象 URL”的标签不正确。

**重现**重现行为的步骤:

1.  转到“设置”
2.  向下滚动到“乳齿象网址”
3.  参见标签

**预期行为**标签应该更通用，例如“Fediverse URL”或“Fediverse Handle”。

**附加内容**乳齿象是唯一一个连接到名为“Fe diversity”的网络的软件。其他包括 Pleroma、Micro.blog、Microblog.pub、Friendica、Osada、Hubzilla 和 Kroeg。更多信息，请参见[fevariable . network](https://fediverse.network)或[fevariable . party](https://fediverse.party)。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1607)

*   [@ben](https://dev.to/ben) 请求了一个我们支持 CodePen Prefill 嵌入的特性。本期详情:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 支持 Codepen 预填充嵌入 #1620](https://github.com/thepracticaldev/dev.to/issues/1620) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-01-21T21:47:19Z">Jan 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1620)

**您的功能请求是否与某个问题相关？请描述一下。**作为一名用户，我希望能够嵌入 codepen 片段，而不必离开我的开发编辑器上下文。Codepen 有一个很酷的新特性支持这一点:

[https://blog . code pen . io/2019/01/17/introducing-prefill-embeds/](https://blog.codepen.io/2019/01/17/introducing-prefill-embeds/)

**描述你想要的解决方案**我想这和我们做`runkit`标签的方式类似。

大约

```
{% codepen %}
this is the code
{% endcodepen %} 
```

我们得到了类似的东西，我们用博客文章指定的正确的 html 标签包装它。我想标签可以知道`{% codepen appropriate_id %}`和可执行体之间的区别，就像 Runkit 已经使用 preamble 一样。

相关的，我相信 Runkit 在评论中不能正常工作的问题还在继续。可以同时被调查。[https://github.com/thepracticaldev/dev.to/issues/1163](https://github.com/thepracticaldev/dev.to/issues/1163)

有关如何使用液体标签的信息:[https://docs.dev.to/liquid-tags/](https://docs.dev.to/liquid-tags/)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1620)

*   [@jess](https://dev.to/jess) 记下了我们遇到的一些延迟作业错误:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)【1621 号](https://github.com/thepracticaldev/dev.to/issues/1621)

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-01-22T01:31:55Z">Jan 22, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1621)

**描述 bug**

我们看到这种类型的错误在执行延迟的作业时弹出。

`Delayed::PerformableMethod (id=#####) FAILED permanently with Delayed::DeserializationError: ActiveRecord::RecordNotFound`

可以通过删除一篇有很多评论的文章，任何触发大量回调的东西来重新创建。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1621)

*   [@sabatesduran](https://dev.to/sabatesduran) 请求了一个我们有阅读位置指示器的功能。谢谢， [@sabatesduran](https://dev.to/sabatesduran) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 读数位置指示器 #1626](https://github.com/thepracticaldev/dev.to/issues/1626) 

[![sabatesduran avatar](img/b830ba8a872918628cbc5a17a55e2a17.png)](https://github.com/sabatesduran) **[sabatesduran](https://github.com/sabatesduran)** posted on [<time datetime="2019-01-22T18:45:37Z">Jan 22, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1626)

**您的功能请求是否与某个问题相关？**这不是问题，但我觉得你有这个栏告诉你文章结束前的滚动量很好。

**描述你想要的解决方案**我的解决方案是在导航中的`#top-bar`内添加一个进度条，根据滚动的当前位置，它会改变这个进度条的百分比。这个想法也将是酒吧的颜色是相同的用户配置文件的颜色，在我的情况下深绿色。

**如何看一个例子？(底部是 GIF)**我创建了这个代码片段，你可以添加到 Chrome Dev Tools > Sources >片段中，并使用`Ctrl+Enter`执行它(至少在 Linux 中)，注意你需要在帖子/文章页面中才能工作。

```
const addProgressBarCSS = () => {
  let css = document.createElement('style');
  css.type = 'text/css';
  css.innerHTML = `
 progress::-webkit-progress-bar {
 background-color: transparent;
 }
 progress::-webkit-progress-value {
 background-color: #0D4D4B;
 }
 progress::-moz-progress-bar {
 background-color: #0D4D4B;
 }
 `;
  document.querySelector("head").appendChild(css);
}

addProgressBarCSS()

let topBar = document.querySelector("#top-bar")
let articleElement = document.querySelector("article");

let readingBarElement = document.createElement("progress");
readingBarElement.id = "reading-bar";
readingBarElement.setAttribute("style", `
 width: 100%;
 position: absolute;
 height: 5px;
 top: 49px;
 bottom: 20px;
 left: 0;
 right: 0;
 -webkit-appearance: none;
 -moz-appearance: none;
 appearance: none;
 border: none;
 background-color: transparent;
 color: #0D4D4B;
`);
readingBarElement.setAttribute("value", pageYOffset);
readingBarElement.setAttribute("max", articleElement.getBoundingClientRect().height);

topBar.appendChild(readingBarElement);

window.addEventListener('scroll', () => {
    let currentScrollPosition = pageYOffset;
    let readingBar = document.querySelector("#reading-bar");
    let article = document.querySelector("article");
    let articlePositions = article.getBoundingClientRect();
    let start = articlePositions.y;
    let end = articlePositions.height;

    if (currentScrollPosition >= end) {
        readingBar.value = end;
    } else if (currentScrollPosition > start) {
        readingBar.value = currentScrollPosition;
    } else {
        readingBar.value = 0;
    }
});
```

Enter fullscreen mode Exit fullscreen mode

**截图和 GIF 说明** [Gif 链接](https://imgur.com/a/dKPv1cZ)

[![image](img/7d114d3199cb8060e1cca7923d24d1cb.png)](https://user-images.githubusercontent.com/5699145/51559460-38db7900-1e82-11e9-9882-6cffd10a970e.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1626)

*   [@韵](https://dev.to/rhymes)注意到被删除的帖子仍然出现在阅读列表中。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 被删除的帖子仍然出现在阅读列表中【1641 号](https://github.com/thepracticaldev/dev.to/issues/1641)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-01-24T14:38:02Z">Jan 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1641)

**描述 bug**

已删除的帖子出现在阅读列表中，我无法手动删除它。

**重现**重现行为的步骤:

一个开发者用户创建了一篇文章，其他人把它添加到他们的阅读列表中。帖子的作者删除了帖子。阅读列表的所有者仍然可以看到列表中的文章。

**预期行为**

删除的文章应该从阅读列表中删除。

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器:Firefox
*   版本:65

**附加上下文**

这也可能与 https://github.com/thepracticaldev/dev.to/issues/1061 有关

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1641)

*   [@ben](https://dev.to/ben) 请求了一个功能搜索词自动完成。评论里有一个人很酷的实现；请查看以下内容:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 搜索词自动完成 #1643](https://github.com/thepracticaldev/dev.to/issues/1643) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-01-24T18:59:18Z">Jan 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1643)

**您的功能请求是否与某个问题相关？请描述一下。**用户应该能够在主网站搜索栏中输入建议的搜索词。

**描述您想要的解决方案**我们希望在用户键入时显示建议的搜索完成。一旦我们有了这个，我们可能会在下拉列表中增加搜索结果的建议。但是我们不想做的是*只*显示结果，因为我们希望用户可以跳转到一个专门的结果页面，有额外的过滤器和选择列表，比如谷歌等。我认为如果自动完成功能充满了结果，那么下一页可能会有帮助就不明显了。

实施:

我们已经有了一个搜索栏的组件区域，这个功能可以建立在它的上面:`app/javascript/src/components/Search`。导航条的 CSS 在这里:`app/assets/stylesheets/top-bar.scss`

Algolia 目前在我们的组件化 JS 的上下文之外是全球可用的，所以访问 Algolia API 类似于我们在编辑器中的标记自动完成:

```
    const algoliaId = document.querySelector("meta[name='algolia-public-id']")
      .content;
    const algoliaKey = document.querySelector("meta[name='algolia-public-key']")
      .content;
    const env = document.querySelector("meta[name='environment']").content;
    const client = algoliasearch(algoliaId, algoliaKey);
    this.index = client.initIndex(`Tag_${env}`);
```

Enter fullscreen mode Exit fullscreen mode

在 Ruby 方面，我们有一个`SearchKeyword`模型，目前只用于少数几个目的，但这是我们希望索引显示的表。我们需要通过`algolia-rails`添加 Algolia 功能，类似于我们在其他地方做的。

这是我们目前在该表上保存的数据:

```
    t.datetime "created_at", null: false
    t.datetime "google_checked_at"
    t.integer "google_difficulty"
    t.integer "google_position"
    t.string "google_result_path"
    t.integer "google_volume"
    t.string "keyword"
    t.datetime "updated_at", null: false
```

Enter fullscreen mode Exit fullscreen mode

谷歌信息只是用于帮助我们确定关键词排名的其他相关细节。“关键字”字符串在这里可能是最重要的。

除了这个公关，我们仍然有额外的优秀元素，改善功能的结果页面。从这里可以看出:

[https://github.com/thepracticaldev/dev.to/issues/797](https://github.com/thepracticaldev/dev.to/issues/797)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1643)

*   [@ben](https://dev.to/ben) 详细介绍了一个功能，当你达到一个新的里程碑时，我们会发送积极的通知:

    # [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 里程碑通知 #1647](https://github.com/thepracticaldev/dev.to/issues/1647) 

    [![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-01-24T23:15:02Z">Jan 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1647)

    *   你的帖子刚刚达到 100 个反应！
    *   你的帖子浏览量刚刚达到 1000 次！
    *   等等。

    作为一名用户，我希望在某些重要事件发生时得到通知。至于何时通知，我认为用二进制升级的方式会很有趣。

    也许开始在 64 个反应时通知人们，然后是 128、256、512、1024 等等。

    对于浏览量，也许从 1，024 开始，然后从那里开始。

    如果奇数太怪异，我仍然认为加倍在频率方面是有意义的。1000、2000、4000、8000、16000 等。

    这可以实现为在反应和/或页面视图更新等之后运行的脚本。并且如果相关的一个奖励了相关的通知，则奖励一个里程碑。

    这将是对徽章的补充，你可以因为某些成就获得一次徽章。但这只是一个通知。

    ```
    </div>
    <div class="gh-btn-container"><a class="gh-btn" href="https://github.com/thepracticaldev/dev.to/issues/1647">View on GitHub</a></div> 
    ```

    Enter fullscreen mode Exit fullscreen mode

*   @itsjzt 为帖子请求了一个“在 WhatsApp 上分享”按钮。谢谢， [@itsjzt](https://dev.to/itsjzt) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在 whatsapp 上分享帖子 #1654](https://github.com/thepracticaldev/dev.to/issues/1654) 

[![itsjzt avatar](img/112257a952ab28cb5c1dc6a703e8ae8a.png)](https://github.com/itsjzt) **[itsjzt](https://github.com/itsjzt)** posted on [<time datetime="2019-01-26T03:11:04Z">Jan 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1654)

目前，我们可以在 Twitter、Reddit、linkedin 和脸书上分享帖子。

我们能在 WhatsApp 上分一杯羹吗选项，WhatsApp 有十亿用户，会有很多人在 WhatsApp 上分享 dev.to 帖子。

我很想自己制作那个特写。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1654)

*   [@pranayairan](https://dev.to/pranayairan) 提出了`/new`帖子默认模板可以改进的观点。谢谢，[@调息风](https://dev.to/pranayairan)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 新增帖子默认模板可改进 #1655](https://github.com/thepracticaldev/dev.to/issues/1655) 

[![pranayairan avatar](img/aac0004c9ede1584418d6df1502dae5c.png)](https://github.com/pranayairan) **[pranayairan](https://github.com/pranayairan)** posted on [<time datetime="2019-01-26T22:03:59Z">Jan 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1655)

**您的功能请求是否与某个问题相关？请描述一下。**我最近想在 dev.to 上添加一个新帖子，我去了 https://dev.to/new[并阅读了帮助部分。我从标题、标签等开始。我认为描述是为我的帖子添加细节的地方。我在那里添加了所有细节。但是当我点击预览时，我只能看到文章的标题。](https://dev.to/new)

在尝试了多次之后，我还是不知道该做什么。我在 twitter 上联系了 Practical Dev，和他聊过之后，我觉得我可能需要在下面添加这些细节，顶部只是元数据，描述不是放置所有内容的地方。

**描述你想要的解决方案**我能想到两件事，能有所帮助。

1.  在帮助中放一个示例，带有 hello world 文本和所有可能的内容。
2.  更改新帖子的默认模板，在横线下包含 hello world 文本，让人们知道内容在那里。

**描述你考虑过的其他选择**没有其他选择，以上两个选项都可以。

**附加上下文**示例模板:

```
---
title: 
published: false
description: 
tags: 
---
Content goes here !! 
```

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1655)

*   [@thomasbnt](https://dev.to/thomasbnt) 报告了一个将鼠标悬停在帖子右侧边栏的“CTA”按钮上的错误。谢谢， [@thomasbnt](https://dev.to/thomasbnt) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将按钮悬停在工具条上 #1672](https://github.com/thepracticaldev/dev.to/issues/1672) 

[![thomasbnt avatar](img/f60db522d165149e9ae5fd2a96d0272c.png)](https://github.com/thomasbnt) **[thomasbnt](https://github.com/thomasbnt)** posted on [<time datetime="2019-01-28T20:09:15Z">Jan 28, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1672)

**描述 bug** 侧边栏按钮的悬停有点受压。

[链接到示例的帖子](https://dev.to/devteam/the-7-most-popular-dev-posts-from-the-past-week-4kn7)

**要重现**悬停按钮。

**预期行为**焦点和悬停时没有 bug。

**截图** [![bug_css_devto_28012019](img/c17c94f7a1b07c55a0a0697da3b60fc1.png)](https://user-images.githubusercontent.com/14293805/51863078-6328ad00-2340-11e9-8666-61bf9f757861.gif)

**桌面(请填写以下信息):**

*   操作系统: **Ubuntu 16.04**
*   浏览器:铬
*   版本:**版本 71.0.3578.98(正式版)(64 位)**

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1672)

*   @jess 请求我们在标签页面的侧边栏添加标签版主的功能。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加标签版主到侧边栏 #1677](https://github.com/thepracticaldev/dev.to/issues/1677) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-01-28T22:32:10Z">Jan 28, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1677)

**描述你想要的解决方案**让人们知道每个标签的版主是谁会更好。我们或许可以在标签边栏上为它找到一个位置。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1677)

*   @rasmusvhansen 报告了一个 bug，在这个 bug 中，嵌入了多个 gist 的帖子会混淆 gist 的顺序。谢谢， [@rasmusvhansen](https://dev.to/rasmusvhansen) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 有多个嵌入 gists 的帖子有顺序混淆 #1679](https://github.com/thepracticaldev/dev.to/issues/1679) 

[![rasmusvhansen avatar](img/251b655b824f6561c4140d5af8f32dd2.png)](https://github.com/rasmusvhansen) **[rasmusvhansen](https://github.com/rasmusvhansen)** posted on [<time datetime="2019-01-29T07:52:54Z">Jan 29, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1679)

**描述一下我的帖子【https://dev.to/rasmusvhansen/rxjs-transducer T2】上的 bug**-harness-the-power-of-rxjs-operators-1ai 8 我在页面中嵌入了 5 个 gists。我注意到要点的顺序是不固定的。在一些重新加载中，第一个和第三个 gists 被交换。

其他用户也注意到了这一点(见帖子上的评论)

**重现**重现行为的步骤:

注意:这有点难以复制。我看了两次，但不能始终如一地重现。

1.  转到[https://dev.to/rasmusvhansen/rxjs-transducer-驾驭-权力的-rxjs-运营商-1ai8](https://dev.to/rasmusvhansen/rxjs-transducer---harness-the-power-of-rxjs-operators-1ai8)
2.  检查列表的顺序
3.  重新加载并检查订单是否相同
4.  重复步骤 3，直到出现错误

**预期行为**gist 的顺序应该是 markdown 中指定的，而不是随机的。

**截图**如果适用，添加截图来帮助解释您的问题。

**桌面(请填写以下信息):**

*   操作系统:Windows 10
*   浏览器:铬
*   版本:71

**智能手机(请填写以下信息):**

*   设备:iPhone 6.1
*   OS: iOS 12.1
*   浏览器旅行

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1679)

*   @jahirfiquitiva 提出了一个问题，代码片段的可访问性可以改进，特别是文本大小。谢谢， [@jahirfiquitiva](https://dev.to/jahirfiquitiva) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 提高代码片段的可访问性 #1685](https://github.com/thepracticaldev/dev.to/issues/1685) 

[![jahirfiquitiva avatar](img/1311e369380698e7b5d113ee6394bef4.png)](https://github.com/jahirfiquitiva) **[jahirfiquitiva](https://github.com/jahirfiquitiva)** posted on [<time datetime="2019-01-30T01:54:55Z">Jan 30, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1685)

**描述 bug** 我觉得代码片段有点难读，我阿发克不是色盲。我不知道这对真正的人来说有多难。

**重现**基本上检查任何代码片段

**预期行为**使用具有良好对比度的颜色。

**截图**

**代码片段** [![captura de pantalla 2019-01-29 a la s 9 39 41 a m](img/0965b0803d35f69635374ec65523fac8.png)](https://user-images.githubusercontent.com/10360816/51952849-f24cc800-2407-11e9-8abf-4b8afee6bb81.png)

[![captura de pantalla 2019-01-29 a la s 9 42 28 a m](img/0ea29638cdce7b0feca1a4e5be01bae3.png)](https://user-images.githubusercontent.com/10360816/51952845-f1b43180-2407-11e9-99b1-c8cc9e5cc290.png)[![captura de pantalla 2019-01-29 a la s 9 41 50 a m](img/0f944467204c0fa01da08e5be606c185.png)](https://user-images.githubusercontent.com/10360816/51952846-f1b43180-2407-11e9-99f9-2b76f65d2331.png)[![captura de pantalla 2019-01-29 a la s 9 41 27 a m](img/e2d15a7238ec878ca8822b0373f540ed.png)](https://user-images.githubusercontent.com/10360816/51952848-f1b43180-2407-11e9-864b-f98f53da1e90.png)

**桌面(请填写以下信息):**

*   操作系统:MacOS Mojave
*   浏览器:铬
*   版本:72.0.3626.81

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1685)

*   [@giorgosk](https://dev.to/giorgosk) 报告了一个问题，一篇文章可能会在 4 篇文章相关区域中显示为相关文章。谢谢， [@giorgosk](https://dev.to/giorgosk) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 帖子本身作为相关帖子出现在  #1697](https://github.com/thepracticaldev/dev.to/issues/1697) 四帖相关区

[![GiorgosK avatar](img/ca338fc85cb2f6f7674f753deb7676a9.png)](https://github.com/GiorgosK) **[GiorgosK](https://github.com/GiorgosK)** posted on [<time datetime="2019-01-31T07:46:44Z">Jan 31, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1697)

**描述这个 bug** 它本身不是一个 bug，但是我刚刚在[的这个帖子](https://dev.to/wolksoftware/why-typescript-is-a-better-option-than-javascript-when-it-comes-to-functional-programming-3mp0)上注意到，同一个帖子显示为它自己的相关帖子。

**要重现**请尝试使用上面的 URL 访问特定文章。也许这是不可复制的，因为当我下一次从不同的浏览器访问 URL 时，这篇文章在 4 篇文章区域的不同位置。

**预期行为**也许这个空间更适合实际相关的职位

**截图** [![4-post-area](img/6168ec09146ff199e79fb297e2bb54d8.png)](https://user-images.githubusercontent.com/520704/52039345-f0385580-253c-11e9-8a4f-080c7883bfeb.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1697)

*   [@deyshin](https://dev.to/deyshin) 提出了“关注/不关注”状态图标令人困惑的讨论。谢谢， [@deyshin](https://dev.to/deyshin) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 跟随/不跟随状态图标令人困惑 #1701](https://github.com/thepracticaldev/dev.to/issues/1701) 

[![deyshin avatar](img/c30c88b9d74198494e72d7494f17cce8.png)](https://github.com/deyshin) **[deyshin](https://github.com/deyshin)** posted on [<time datetime="2019-01-31T17:27:34Z">Jan 31, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1701)

**您的功能请求是否与某个问题相关？请描述一下。**在注册过程中，建议关注的人旁边有`+`标记。选择它们会将其更改为复选标记。对我个人来说，`+`的对面状态是`-`，选中的对面是未选中。我花了足够多的时间来自己做决定，说什么是被选中的，什么不是。

现在我意识到所有的个人资料页面都遵循相同的模式`+`并被检查。我们可以重新考虑这个设计选择吗？

**描述你想要的解决方案**我觉得要么是`+`、`-`对，要么是勾选、未勾选对。此外，`+`是三衬线，而复选标记是衬线风格也有点混乱。

**描述你考虑过的替代方案**习惯就好？个人资料页面看起来并不混乱，因为它在图标旁边写着`following`。除非另有说明，否则仍然令人困惑。[![image](img/25a9fbcabb42e386da5f883574ace63b.png)](https://user-images.githubusercontent.com/8847894/52072530-2385df00-2553-11e9-9a48-7805c3124790.png)[![image](img/a509e5364dbb6c7b309c987cda5324a9.png)T8】](https://user-images.githubusercontent.com/8847894/52072557-33052800-2553-11e9-9d5a-e1423219b54e.png)

**附加上下文**不适用

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1701)

*   [@aracki](https://dev.to/aracki) 提出了如何提高 dev.to 的 SEO 的讨论。谢谢， [@aracki](https://dev.to/aracki) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Dev.to 更好的站点排名(SEO)？ #1707](https://github.com/thepracticaldev/dev.to/issues/1707) 

[![Aracki avatar](img/cc9d71a4353c83d4bd34bc8e0078210f.png)](https://github.com/Aracki) **[Aracki](https://github.com/Aracki)** posted on [<time datetime="2019-02-01T14:30:54Z">Feb 01, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1707)

我注意到 dev.to 在开发者中越来越受欢迎，但 Medium articles 在搜索引擎上的排名仍然要好得多。

怎样做才能提高开发人员的站点排名？

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1707)

*   [@aligoren](https://dev.to/aligoren) 提出了外部链接是否应该在新标签页打开的讨论。谢谢@阿里戈伦！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加用户设置外部链接如何打开 #1716](https://github.com/thepracticaldev/dev.to/issues/1716) 

[![aligoren avatar](img/aaddb955a894663ad720ed21607443ae.png)](https://github.com/aligoren) **[aligoren](https://github.com/aligoren)** posted on [<time datetime="2019-02-02T19:47:03Z">Feb 02, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1716)

**您的功能请求是否与某个问题相关？请描述一下。**

有些帖子像[这个](https://dev.to/aligoren/nice-vue-ui-toolkit-repositories-on-github-l9c)包含外部链接。当你阅读一篇文章时，你点击一个外部链接。在你读完这篇文章之前，你正在重定向到外部链接。

如果你没有使用浏览器扩展，这可能是一个不好的体验。

**描述您想要的解决方案**

我不擅长 Ruby。也许这在 Ruby 中很容易处理。但是正如我所说的，我对 Ruby 没有任何经验。所以，我的解决方案是用 Javascript。：

```
const linksOpenNewTab = () => {
    const links = document.querySelectorAll("#article-body a")

    Array
        .from(links)
        .filter(filteredLink => !filteredLink.href.includes(window.location.href))
        .forEach(link => {
            link.setAttribute("target", "_blank")
            link.setAttribute("rel", "noopener noreferrer")
        })
}

linksOpenNewTab()
```

Enter fullscreen mode Exit fullscreen mode

**描述你考虑过的替代方案**

我不知道。但是 Rails 中应该有很多解决方案。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1716)

过去两周就这样了！感谢阅读！