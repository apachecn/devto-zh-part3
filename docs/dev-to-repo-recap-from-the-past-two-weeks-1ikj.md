# 开发人员对过去两周的回购进行总结

> 原文：<https://dev.to/devteam/dev-to-repo-recap-from-the-past-two-weeks-1ikj>

欢迎回到另一个回购回顾，在这里我们(通常)涵盖上周对 [dev.to 的仓库](https://github.com/thepracticaldev/dev.to)和[iOS 回购](https://github.com/thepracticaldev/dev-ios)的贡献。这一期涵盖 4 月 5 日至 4 月 19 日。

# 特性

*   我们推出了`https://dev.to/videos`！这是所有上传到 DEV 上的视频的登陆页面。感谢[@杰斯](https://dev.to/jess)让它出来！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【视频】 #2291](https://github.com/thepracticaldev/dev.to/pull/2291) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-04-02T20:51:42Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2291)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

添加一个页面(/videos)以查看包含本地视频内容的所有帖子。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2291)

*   来自 GitPod 团队的 [@jankeromnes](https://dev.to/jankeromnes) 增加了一个在你的浏览器中运行的一键式开发环境！这大大降低了回购的门槛。谢谢， [@jankeromnes](https://dev.to/jankeromnes) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 使用 Gitpod 的自动化开发设置 #2321](https://github.com/thepracticaldev/dev.to/pull/2321) 

[![jankeromnes avatar](img/679a1be9e6f8a066b795a0663f37d93f.png)](https://github.com/jankeromnes) **[jankeromnes](https://github.com/jankeromnes)** posted on [<time datetime="2019-04-07T16:53:24Z">Apr 07, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2321)

<g-emoji class="g-emoji" alias="wave" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44b.png">👋</g-emoji>你好！

我在 gitpod.io 上工作，这是 GitHub 的一个点击式在线 IDE，我想为 dev 提供一个几乎全自动的开发设置。 <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡️</g-emoji>

请让我知道你的想法。

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

在这个 PR 中，我将 Gitpod 配置为:

*   预安装 PostgreSQL 和正确的 Ruby 版本(通过`.gitpod.dockerfile`)
*   用`config/application.yml`初始化开发工作区并运行`bin/setup`
*   自动启动`bin/startup`并在预览中打开应用程序

它看起来是这样的:

[![Open in Gitpod](img/e87a122edd69b5091fe1b24b5a2c8d0a.png) ](https://gitpod.io/#https://github.com/jankeromnes/dev.to) [ ![Screenshot 2019-04-07 at 15 00 01](img/3a4a7d79312eab2f1810e150e89ab657.png)](https://user-images.githubusercontent.com/599268/55684989-ae1efc00-5951-11e9-9087-4a32d694e3c6.png)

## 相关车票&单据

(无)

## 手机&桌面截图/录音(如有 UI 改动)

唯一非自动化的部分是 Algolia 凭证，Gitpod 在启动时会像这样要求:

[![Screenshot 2019-04-07 at 14 55 54](img/367fafd70f277be7a97eb1d09815b918.png)](https://user-images.githubusercontent.com/599268/55684982-a19aa380-5951-11e9-8a09-cfcd1318b21e.png)

## 添加到文档中？

*   [ ]文档开发到
*   [x] readme
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![frictionless coding](img/5d82a501a2ce2dacf0b203b67cae7a3c.png)](https://camo.githubusercontent.com/e1f9fd85b803a5ad3c675051c2446dc20f62ef8e/68747470733a2f2f692e67697068792e636f6d2f6d656469612f6c4a4e6f424376515970376e712f67697068792e77656270)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2321)

*   我们现在有一个关于我们社区版主的官方页面！谢谢， [@jess](https://dev.to/jess) 看看这里:【https://dev.to/community-moderation】T2

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 社区审核页面 #2364](https://github.com/thepracticaldev/dev.to/pull/2364) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-04-10T19:50:03Z">Apr 10, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2364)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

创建社区审核页面和更新标签审核页面。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2364)

*   [@mariocsee](https://dev.to/mariocsee) 在代码沙箱 embed/Liquid 标签中增加了“点击运行”功能。谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 允许 codesandbox 接受 runonclick  #2381](https://github.com/thepracticaldev/dev.to/pull/2381) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-11T16:15:14Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2381)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]功能

## 描述

添加了`runonclick`作为 codesandbox liquid 标签的选项。添加`runonclick=1`允许用户让查看器决定 codesandbox 何时运行。

## 相关车票&单据

#2290 的解决方法

## 手机&桌面截图/录音(如有 UI 改动)

[![Screen Shot 2019-04-11 at 12 12 23](img/6837df887887faebc5c9d5c15316ded0.png)](https://user-images.githubusercontent.com/13403332/55973232-1e2fc980-5c53-11e9-9d1d-c7b88c07610e.png)

## 添加到文档中？

*   [x] `p/editor_guide`

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2381)

*   @andrewbrown 增加了一个很受欢迎的功能，可以对你的[仪表盘](https://dev.to/dashboard)进行排序。谢谢， [@andrewbrown](https://dev.to/andrewbrown) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 能够对仪表盘文章进行排序(#1837)  #2337](https://github.com/thepracticaldev/dev.to/pull/2337) 

[![omenking avatar](img/c5b3096d42a99b0bcad72567d37cd4a0.png)](https://github.com/omenking) **[omenking](https://github.com/omenking)** posted on [<time datetime="2019-04-08T16:47:35Z">Apr 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2337)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

```
Given I am a logged in user
And I am on my dashboard
And I am viewing articles
When I sort by articles by X
Then it should sort by X 
```

## 相关车票&单据

在最初的标签中，我提供了一个我如何实现这个特性的详细旅程:[https://github.com/thepracticaldev/dev.to/issues/1837](https://github.com/thepracticaldev/dev.to/issues/1837)

在`Dashboards#show`中，有文章和关注者代码。我想知道 follower 代码是否还在使用，因为在`#show`动作下面有两个动作更合适命名。

我建议重构这个`#show`动作，只关注功能，增加动作和路线，因为这里测试 perceptive 的内容太多，而 linter 的限制不允许优雅的重构。

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![As they fly by, fly so high](img/c08019db137f1240e18683b47f15cf8f.png)](https://camo.githubusercontent.com/b94bba7e273b4ce1780bf1f37e2d1a56383ed2c5/68747470733a2f2f36362e6d656469612e74756d626c722e636f6d2f34336266316162343730653537356338626636393463323862313263633636322f74756d626c725f6e65307838387075625531746f6f796d706f315f3430302e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2337)

*   [@ben](https://dev.to/ben) 更新了 16 周徽章消息，声明这是你能获得的最后一个徽章。更多细节请看下面的讨论。谢谢你，本！

[![mortoray](img/d44da21f46b8ea3a9ef11fbb09c57f91.png)](/mortoray) [## 你对追逐互联网积分、徽章以及一切的游戏化有什么感受？

### edA QA mort ora y Apr 3 ' 192min read

#discuss](/mortoray/how-do-you-feel-about-chasing-internet-points-badges-and-the-gamification-of-everything-1l56)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 调整徽章连胜复制为 16 周徽章 #2467](https://github.com/thepracticaldev/dev.to/pull/2467) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-16T19:18:32Z">Apr 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2467)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这清楚地表明，16 周徽章是最后的连胜徽章，以消除人们过度劳累或引入游戏化负面影响的可能性。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2467)

*   [@mariocsee](https://dev.to/mariocsee) 在你的浏览器控制台添加了一个小复活节彩蛋。；)谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 控制台消息 ascii 艺术&消息回购 #2392](https://github.com/thepracticaldev/dev.to/pull/2392) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-11T21:26:26Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2392)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]功能

## 描述

将来自`bin/setup`的不带反勾号的开发 ascii 艺术添加到控制台，并显示一条短消息，告诉访问者我们是开源的，并显示到这个 GitHub repo 的链接。

目前的信息是:“嘿，你好！对 dev.to 背后的代码感兴趣？你很幸运——我们是开源的！来打个招呼，告诉我们你的问题，甚至在我们的回购中伸出援手-[https://github.com/thepracticaldev/dev.to/](https://github.com/thepracticaldev/dev.to/)。欢迎建议/改变。<g-emoji class="g-emoji" alias="grinning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f600.png">😀</g-emoji>

我在其他网站上遇到了一些控制台消息，这些消息带有他们职业页面的链接。我觉得很可爱，可能会引导一些新人进入回购。

## 相关车票&单据

不适用的

## 手机&桌面截图/录音(如有 UI 改动)

[![Screen Shot 2019-04-11 at 17 23 36](img/8044385df3f146413a2db45b886f8cc4.png)](https://user-images.githubusercontent.com/13403332/55994046-ab3c4800-5c7e-11e9-8858-e04147689b51.png)

## 添加到文档中？

*   [x]不需要文档

## 哪个 gif 最能描述这次公关或者给你的感觉？

[![oh hi mark](img/85333ad856a0e63a7ac44a8497f9bf3f.png)](https://camo.githubusercontent.com/1396bd57471a2f52c231365cbef0b1a4c6caa5f5/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f66694556533157314d39576d492f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2392)

*   我们现在有开放的收件箱！这实际上是将您的 Connect chat 收件箱向公众开放。感谢马里奥的公关！查看他的更新日志，了解所有细节:

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/76d98b89fcf311531b8c915930cd58be.png)](/devteam) [## 变更日志:开发连接打开收件箱！

### 开发团队的 Mario See 2019 年 4 月 18 日 2 分钟阅读

#changelog #meta](/devteam/changelog-dev-connect-open-inbox-4ab1)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 发布打开收件箱 #2468](https://github.com/thepracticaldev/dev.to/pull/2468) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-16T19:57:44Z">Apr 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2468)

## 这是什么类型的公关？

*   [x]重构

## 描述

*   让用户能够在`/settings/misc`中的`private`和`open`之间改变收件箱的类型。
*   添加了在 connect 中单击 block 时的消息，说明 Block 仅在 Connect 中实现，而不是在整个 DEV 中实现。

## 相关车票&单据

继续关闭#1563 和#2074，解决#1567

## 手机&桌面截图/录音(如有 UI 改动)

### `/settings/misc`

[![Screen Shot 2019-04-16 at 15 48 56](img/6c3879133c9071979c14978f92287879.png)](https://user-images.githubusercontent.com/13403332/56239944-31ca9e00-6060-11e9-88ca-31626aef5748.png)

### 新消息模态

[![Screen Shot 2019-04-16 at 15 47 39](img/af505b55a4c8d6e5aa940bbd2453de2c.png)](https://user-images.githubusercontent.com/13403332/56239964-3bec9c80-6060-11e9-92c1-b827699783ed.png)

### `/connect`

[![Screen Shot 2019-04-16 at 15 47 54](img/f0e3895acfa94cb19a15349e1ae7e3aa.png)](https://user-images.githubusercontent.com/13403332/56239991-4dce3f80-6060-11e9-8042-d0d78ab91404.png)

### 补充澄清

[![Screen Shot 2019-04-16 at 15 48 22](img/a4d097d2d962eb87cebdfa0221fddef2.png)](https://user-images.githubusercontent.com/13403332/56240004-5757a780-6060-11e9-9c22-b4645f5fb094.png)

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2468)

*   [@ben](https://dev.to/ben) 在我们的 API 中添加了一个新的端点来显示热门帖子:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 允许 API 返回置顶文章 #2469](https://github.com/thepracticaldev/dev.to/pull/2469) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-16T21:12:48Z">Apr 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2469)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

允许`/api/articles`根据时间表发布“顶级”文章。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2469)

*   [@edisonywh](https://dev.to/edisonywh) 创建了一个中篇嵌入/液体标签。谢谢， [@edisonywh](https://dev.to/edisonywh) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  #118 允许用户嵌入带有液体标签的中型帖子 #1161](https://github.com/thepracticaldev/dev.to/pull/1161) 

[![edisonywh avatar](img/e6f68256bdd5af6fcd0365cc665cd135.png)](https://github.com/edisonywh) **[edisonywh](https://github.com/edisonywh)** posted on [<time datetime="2018-11-20T14:01:44Z">Nov 20, 2018</time>](https://github.com/thepracticaldev/dev.to/pull/1161)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

*   添加了新的液体标签-" { % medium[https://medium . com }](https://medium.com%7D)
*   增加了一个抓取器- `MediumArticleRetrievalService`,抓取给定的介质 URL 来获取液体标签所需的信息

现在我的实现需要一个完整的链接，但是我们可以选择使用 handle + article ID。在这里接受建议。

## 相关车票&单据

#118

## 手机&桌面截图/录音(如有 UI 改动)

[![11-20-923pm](img/44dcd5000431bc42d14398692a7dba84.png)](https://user-images.githubusercontent.com/20277437/48778152-6e8fa480-ed0f-11e8-8c27-76a567fddc6e.png)

[![11-20-923pm-1](img/d12430d4d166bb68edbd1b890faab1df.png)](https://user-images.githubusercontent.com/20277437/48778162-70f1fe80-ed0f-11e8-9760-e9857e0f4dac.png)

[![03-15-1229AM](img/bc62fb254a60c5d7ed9c8ae332e7cb46.png)](https://user-images.githubusercontent.com/20277437/54374311-d5b7d700-46b9-11e9-8536-d55defebf101.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/1161)

*   [@韵脚](https://dev.to/rhymes)移除了部分 Rails 5.1 特定默认。更多详情请查看公关评论。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新框架默认匹配 Rails 5.1  #2309](https://github.com/thepracticaldev/dev.to/pull/2309) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-04T22:06:58Z">Apr 04, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2309)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

既然代码库已经迁移到了 Rails 5.2，那么升级框架默认值以匹配先前版本应该是安全的。Rails 5.2 的新默认值，请参见[new _ framework _ defaults _ 5 _ 2 . Rb](https://github.com/thepracticaldev/dev.to/blob/master/config/initializers/new_framework_defaults_5_2.rb)稍后可以单独打开。

关于翻转的旗帜:

*   [per_form_csrf_tokens](https://guides.rubyonrails.org/configuring.html#configuring-action-controller) 通过在每个生成的表单中添加 csrf 令牌来提高安全性
*   [伪造 _ 保护 _ 来源 _ 检查](https://guides.rubyonrails.org/configuring.html#configuring-action-controller)检查请求是否来自同一来源
*   `to_time_preserves_timezone`是 Ruby 2.4 和日期时间转换的旧标志
*   如果一个`belongs_to`关联没有被显式声明为可选的，并且在验证过程中丢失，那么[属于 _ 必需 _ 默认](https://guides.rubyonrails.org/configuring.html#configuring-active-record)将无法通过验证(我检查了代码，所有可选关联似乎都被正确声明)
*   [form _ with _ generates _ remote _ forms](https://guides.rubyonrails.org/configuring.html#configuring-action-view)使`form_with`(源代码中未使用)默认生成远程表单

我还重新启用了两个被禁用的 CSRF 令牌，让我知道它们是否应该被搁置(我已经手动测试了它们，它们工作正常)

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2309)

*   [@aspittel](https://dev.to/aspittel) 对 pro 仪表盘进行了修饰，并为图表添加了时间过滤。此外，现在有两个新的图表。谢谢你，阿里！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 特色/过滤图表 #2482](https://github.com/thepracticaldev/dev.to/pull/2482) 

[![aspittel avatar](img/f12052c0aff846de845fda5d2ebdf5f0.png)](https://github.com/aspittel) **[aspittel](https://github.com/aspittel)** posted on [<time datetime="2019-04-18T12:23:25Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2482)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将月/周/无限过滤器添加到 pro 仪表板

*   问题:“无限”实际上应该追溯到多远？

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2482)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 使卡片动态变化并添加读者/追随者图表 #2488](https://github.com/thepracticaldev/dev.to/pull/2488) 

[![aspittel avatar](img/f12052c0aff846de845fda5d2ebdf5f0.png)](https://github.com/aspittel) **[aspittel](https://github.com/aspittel)** posted on [<time datetime="2019-04-18T17:10:00Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2488)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]特性
*   [ ]错误修复
*   [ ]文档更新

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2488)

*   [@ben](https://dev.to/ben) 在桌面 PWA 上增加了一些导航按钮。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 给 pwa 桌面添加导航按钮 #2484](https://github.com/thepracticaldev/dev.to/pull/2484) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-18T15:07:48Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2484)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这仍然是完全实验性的，但是这为桌面 PWA 增加了导航按钮的功能。

[![](img/0d05565c872629343bc3d1a41dd69a7d.png)](https://camo.githubusercontent.com/9c090d4e9bb875cc13370beb05d2370aba976ec3/68747470733a2f2f636c2e6c792f3133663135653562383338342f496d616765253230323031392d30342d3138253230617425323031312e30362e3332253230414d2e706e67)

桌面 PWA 仍然有缺失的功能/未解决的问题，但这是一个有趣的步骤。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2484)

*   [@mariocsee](https://dev.to/mariocsee) 将`phpc.social`添加到我们的乳齿象允许列表中。谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将 phpc.social 添加到乳齿象列表 #2352](https://github.com/thepracticaldev/dev.to/pull/2352) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-09T20:38:31Z">Apr 09, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2352)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

将“phpc.social”添加为可接受的乳齿象 URL。

## 相关车票&单据

解决#2345

## 手机&桌面截图/录音(如有 UI 改动)

不适用的

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2352)

*   [@aspittel](https://dev.to/aspittel) 粉化 DEV！在你的[设置](https://dev.to/settings/misc)中查看她的主题。谢谢你，阿里！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)# 2425](https://github.com/thepracticaldev/dev.to/pull/2425)

[![aspittel avatar](img/f12052c0aff846de845fda5d2ebdf5f0.png)](https://github.com/aspittel) **[aspittel](https://github.com/aspittel)** posted on [<time datetime="2019-04-13T15:01:10Z">Apr 13, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2425)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [X]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

开发人员的粉色模式！

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/d3473f9bcf3ae3644bf877ed79869559.png)](https://camo.githubusercontent.com/df4c6e7bcd4134c94e8e0365b6fd5437d857d298/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6c3045787473555948786f636c4c6538302f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2425)

# Bug 修复/其他贡献

*   [@consou](https://dev.to/consou) 将数据库事务包装在将两个用户合并在一起的代码周围，以实现更安全的流程。谢谢，[@康索](https://dev.to/consou)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)# 2281](https://github.com/thepracticaldev/dev.to/pull/2281)更安全的用户合并 db 交易

[![ConSou avatar](img/c01c6634f4131b80c23ab4ee4fb83a49.png)](https://github.com/ConSou) **[ConSou](https://github.com/ConSou)** posted on [<time datetime="2019-04-01T19:58:47Z">Apr 01, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2281)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

添加数据库事务，以便在出现异常时允许回滚。这将保护数据免受程序错误或数据库崩溃的影响，并有助于更安全地合并用户帐户。

## 相关车票&单据

#2230

## 手机&桌面截图/录音(如有 UI 改动)

不适用的

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2281)

*   [@ben](https://dev.to/ben) 将一些主题相关的代码重构到一个单独的部分中，并添加了主题化 logo 的选项。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将主题配置移动到单独的部分并添加标志主题能力 #2427](https://github.com/thepracticaldev/dev.to/pull/2427) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-13T16:52:45Z">Apr 13, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2427)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将混乱的主题隐藏在它自己的部分中。我们还有进一步的重构要做，但这样更好。还增加了主题标志和其他小的 css 变化的能力。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2427)

*   [@maximepiard](https://dev.to/maximepiard) 清理了标签重量页面的一个问题。谢谢， [@maximepiard](https://dev.to/maximepiard) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 变暗标记跟随重量输入的背景 #2318](https://github.com/thepracticaldev/dev.to/pull/2318) 

[![maximepiard avatar](img/c229be04452dd3323260f3b3bad90e22.png)](https://github.com/maximepiard) **[maximepiard](https://github.com/maximepiard)** posted on [<time datetime="2019-04-06T10:38:12Z">Apr 06, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2318)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这是一个小变化，所以我没有花时间在本地运行这个应用程序。[这篇文章](https://dev.to/devteam/contributors-wanted-night-mode-themes-on-dev-i8n)说可以接受，所以我希望这没问题<g-emoji class="g-emoji" alias="slightly_smiling_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f642.png">🙂</g-emoji>

我现在发现按钮的颜色有点奇怪。 [![image](img/b4231231be1c5fd24f1e352022769f68.png)](https://user-images.githubusercontent.com/13153190/55668267-d3374000-5867-11e9-8fc7-1fe94b8426e2.png) 我还注意到现在没有固定的`--theme-button-color`用于夜间模式，这是一个好主意吗，来取代这个，也许是家庭饲料上的`Save`吗？ [![image](img/7042d78497d32ddb20601e14b1ffb3e8.png)](https://user-images.githubusercontent.com/13153190/55668286-227d7080-5868-11e9-9129-e61b5fc28da5.png) 你怎么看？如果需要，我可以打开一个问题。

## 相关车票&单据

解析[https://github.com/thepracticaldev/dev.to/issues/2184](https://github.com/thepracticaldev/dev.to/issues/2184)

## 手机&桌面截图/录音(如有 UI 改动)

没有截图，因为我没有运行应用程序。只有一种颜色变化，所以应该没问题。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![my first baby PR on dev.to](img/a77ce7ee98a3bf1d5b2fc653ecefa4f4.png)](https://camo.githubusercontent.com/094be97eb085d1e1bc7188637dc4a6add7a787f7/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f3131316c487861694a796e5167382f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2318)

*   @lightalloy 为用户的 GitHub 或 Twitter 用户名添加了条件验证。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 用户 github_username 和 twitter_username 的条件验证#2061  #2312](https://github.com/thepracticaldev/dev.to/pull/2312) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-05T11:51:27Z">Apr 05, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2312)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]优化

## 描述

*   仅当相应字段在登录时被更改为较低的请求量时以及在其他情况下，才验证`github_username`和`twitter_username`的唯一性
*   在这些字段的验证中将`allow_blank`更改为`allow_nil`选项，因为只允许将`nil`写入数据库。

## 相关车票&单据

#2061

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2312)

*   [@abraham](https://dev.to/abraham) 将我们的 Ruby linter rubo COP 更新为 0.67。谢谢， [@abraham](https://dev.to/abraham) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新 rubocop 到 0.67  #2313](https://github.com/thepracticaldev/dev.to/pull/2313) 

[![abraham avatar](img/429531d9f567abaef8022a71b195b789.png)](https://github.com/abraham) **[abraham](https://github.com/abraham)** posted on [<time datetime="2019-04-05T15:00:16Z">Apr 05, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2313)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

*   [x]将 rubocop 更新到 0.67
*   [x]为从 rubocop 宝石中移除的 cop 增加了 [rubocop-performance](https://github.com/rubocop-hq/rubocop/blob/master/manual/migrate_performance_cops.md)
*   [x]修正错误

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2313)

*   [@ben](https://dev.to/ben) 为`/videos`增加了一些测试，清理了一些造型。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 清理视频页面并添加测试 #2315](https://github.com/thepracticaldev/dev.to/pull/2315) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-05T19:24:46Z">Apr 05, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2315)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

从最初的`/videos`推送和添加测试中清理一些功能。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2315)

*   [@韵脚](https://dev.to/rhymes)解决了一些`Style`相关的 Rubocop 违规。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修正了一些风格/* rubocop 违规 #2314](https://github.com/thepracticaldev/dev.to/pull/2314) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-05T18:57:10Z">Apr 05, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2314)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   []错误修复
*   [ ]文档更新

## 描述

修正了一些“Style/*”rubo COP 的违规，并增加了一些测试

## 相关车票&单据

参见#2021

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2314)

*   [@perlatsp](https://dev.to/perlatsp) 更新了夜间模式的 GitHub 集成设置页面。谢谢， [@perlatsp](https://dev.to/perlatsp) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 集成 github repos 悬停和文本颜色 #2302](https://github.com/thepracticaldev/dev.to/pull/2302) 

[![perlatsp avatar](img/b7d7ddbe805ae27121ea7bc77b9c85be.png)](https://github.com/perlatsp) **[perlatsp](https://github.com/perlatsp)** posted on [<time datetime="2019-04-03T20:44:52Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2302)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [X]错误修复
*   [ ]文档更新

## 描述

修复。在设置/集成页面上，当你选择了 GitHub 库时，默认选择的将是白色背景和颜色，这使得它不可读。这将使颜色为黑色的口音，并将添加一个悬停效果改变背景为白色。

## 以前

[![Before](img/56352787c6663a43e669ef95aa1b8f5b.png)](https://user-images.githubusercontent.com/10896363/55511852-7f570c00-5659-11e9-9be8-7ae388536af6.png)

## 后

[![After](img/829bcf7e8c5fb3d10ffa7536baf1f198.png)](https://user-images.githubusercontent.com/10896363/55511872-8e3dbe80-5659-11e9-8de7-0dd270958620.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2302)

*   [@co16353sidak](https://dev.to/co16353sidak) 在夜间模式下清理了评论区，特别是任何带有`fluid-comments` HTML 类的内容。谢谢， [@co16353sidak](https://dev.to/co16353sidak) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 固定流体-黑暗模式下的评论 #2246](https://github.com/thepracticaldev/dev.to/pull/2246) 

[![co16353sidak avatar](img/f8a7df09f60d579a8a2f71944fcdcef0.png)](https://github.com/co16353sidak) **[co16353sidak](https://github.com/co16353sidak)** posted on [<time datetime="2019-03-31T12:23:10Z">Mar 31, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2246)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

黑暗模式中的内嵌流体注释现在与默认主题中遵循的语法相匹配。

我无法设置一个本地环境(面临一些问题，我没有太多的 ruby 经验)来测试我所做的改变，但我已经尽我所能对 css 做了一些改变，希望本地开发环境不会有问题。我已经能够使用 firefox 中的开发工具测试更改，我已经包括了相同的截图。

`fluid-comment`类现在以与默认模式相同的方式为黑暗模式工作。

修复#2221

## 手机&桌面截图/录音(如有 UI 改动)

默认模式: [![Screenshot 2019-03-31 at 5 47 19 PM](img/e8f9722d24f0f414a293d66849072747.png)](https://user-images.githubusercontent.com/35738138/55288939-09df0780-53dd-11e9-9fe5-bfa75c12c047.png)

现在处于黑暗模式: [![Screenshot 2019-03-31 at 5 46 29 PM](img/c05681207bf7c4d0b4abd810869d7cf3.png)](https://user-images.githubusercontent.com/35738138/55288931-ed42cf80-53dc-11e9-9e82-4b8fc5088da6.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![don't give up](img/41a43b052d8883cb90eb752c086d6501.png) ](https://camo.githubusercontent.com/b47550a87b48519591baa4895c9be095e467c75c/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f336f3835787338714574697146506a7255632f736f757263652e676966) [ ![first time understanding ruby, so tired](img/b094bf5e8039ec5460ec18c6c5ee4aaa.png)](https://camo.githubusercontent.com/8fff7e0077cf50999f3a6e81a07d90b00fd5d6ea/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6542436e7075524742685147592f67697068792d646f776e73697a65642d6c617267652e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2246)

*   [@lightalloy](https://dev.to/lightalloy) 改进了我们的 Rubocop 配置。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 改进 rubocop 配置#2021  #2324](https://github.com/thepracticaldev/dev.to/pull/2324) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-08T09:54:20Z">Apr 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2324)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   将`Rails/SkipsModelValidations`移至`.rubocop.yml`,因为它不应该被启用
*   修正了宝石的顺序
*   启用了几个没有违规的`Layout`警察

## 相关车票&单据

#2021

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2324)

*   @lightalloy 还通过删除一个额外的 SQL 查询对我们如何记住用户进行了调整。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 不做额外的 SQL 查询记住用户 #2335](https://github.com/thepracticaldev/dev.to/pull/2335) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-08T11:35:38Z">Apr 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2335)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]优化

## 描述

*   设置用户`remember`字段，但不立即写入数据库
*   `AuthorizationService`的基本规格

## 相关车票&单据

#2061

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2335)

*   [@ben](https://dev.to/ben) 通过完全替换库，修复了我们人类语言检测的一个 bug。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 搞定人类语言检测 #2338](https://github.com/thepracticaldev/dev.to/pull/2338) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-08T17:46:30Z">Apr 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2338)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

用 Ruby 版本替换我们的破损语言检测服务。这个可能不太靠谱，但是绝对够靠谱，聊胜于无。

这还增加了主页订阅功能，以支持当前用户的首选语言。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2338)

*   [@cyrillefr](https://github.com/cyrillefr) 用 Rubocop 启用`Layout/AlignParameters` cop/rule。谢谢， [@cyrillefr](https://dev.to/cyrillefr) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用布局/对齐参数 #2340](https://github.com/thepracticaldev/dev.to/pull/2340) 

[![cyrillefr avatar](img/7ef3538029145d4cf304bfb0f0cca6f5.png)](https://github.com/cyrillefr) **[cyrillefr](https://github.com/cyrillefr)** posted on [<time datetime="2019-04-08T19:43:49Z">Apr 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2340)

检查多行方法调用或定义上的参数是否对齐。

决议:#2021

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2340)

*   [@ben](https://dev.to/ben) 在`saving`一个用户进入数据库时删除了一些不必要的查询。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除用户不必要的查询保存 #2342](https://github.com/thepracticaldev/dev.to/pull/2342) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-08T21:43:09Z">Apr 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2342)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

目前，当重新保存用户时，它会进行一系列额外的查询，以便将相关信息插入到 Algolia 中。这会影响尝试重新登录但由于超时而失败的用户。

此 PR 删除了不必要的查询。

我们可以在 Algolia 插入发生的地方进行类似的优化。基本上，插入本身是异步的，但是在插入之前收集相关数据的过程是*而不是*异步的。这里的解决方案是首先去掉我们从来不需要的信息，但是需要在其他模型上有其他需要这些信息的解决方案。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2342)

*   [@lightalloy](https://dev.to/lightalloy) 将用户电子邮件设置为`nil`而不是 Ruby on Rails 的`blank`，在这种情况下这意味着空字符串。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将用户邮箱设置为零而不是空白 #2305](https://github.com/thepracticaldev/dev.to/pull/2305) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-04T12:54:54Z">Apr 04, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2305)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

由于唯一键被添加到`users.email`中，空白值将被视为重复。用户并不总是指定电子邮件，所以我们必须允许数据库中的`null`值，并确保没有人试图将空白电子邮件写入数据库。

## 相关车票&单据

#2061

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2305)

*   @venarius 对 UX 做了一些改进，让页脚变得更宽，同时也做了一些重构。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UX:页脚和小重构宽度更好 #2166](https://github.com/thepracticaldev/dev.to/pull/2166) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-03-21T21:01:07Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2166)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

*   改变了页脚的宽度，现在和文章一样了
*   将通知页面的背景颜色从$浅灰色更改为$最浅灰色
*   从读取列表中删除不必要的`.comments-container`
*   在页脚的年份和火焰表情符号之间添加了`&nbsp`，因为它有时会切换到新的一行

## 相关车票&单据

[https://github.com/thepracticaldev/dev.to/issues/1794](https://github.com/thepracticaldev/dev.to/issues/1794)

## 手机&桌面截图/录音(如有 UI 改动)

*   没有必要，因为这是一个错误修正

通知页面`$light-gray` bug(你必须仔细看才能看到它<g-emoji class="g-emoji" alias="smile" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f604.png">😄</g-emoji>):

[![Screenshot from 2019-03-21 21-45-31](img/c0f32e4a6470a0affbf23b60f5516831.png)](https://user-images.githubusercontent.com/13546486/54784399-0fda2900-4c24-11e9-9d32-76632ba05fbc.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2166)

*   [@押韵](https://dev.to/rhymes)用 Rubocop 启用`Rails/TimeZone` cop/rule。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用轨道/时区 cop  #2334](https://github.com/thepracticaldev/dev.to/pull/2334) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-08T11:32:50Z">Apr 08, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2334)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

在#2324 的简短讨论后，我启用了 cop `Rails/TimeZone`

## 相关车票&单据

#2324 #2021

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2334)

*   [@venarius](https://dev.to/venarius) 使斯隆透明，以适当配合夜晚主题。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UX:让斯隆透明- >现在在夜场工作主题 #2353](https://github.com/thepracticaldev/dev.to/pull/2353) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-09T21:38:16Z">Apr 09, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2353)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

用透明版替换了 sloan.png

## 相关车票&单据

关闭#2349

## 手机&桌面截图/录音(如有 UI 改动)

[![Screenshot from 2019-04-09 23-37-18](img/69a450123320e05f5ca4b5b310a8d5e1.png) ](https://user-images.githubusercontent.com/13546486/55837038-7c0fc480-5b20-11e9-827c-bee20fd15082.png) [ ![Screenshot from 2019-04-09 23-37-41](img/f7ba28b76fe3456d461283bac21af18e.png)](https://user-images.githubusercontent.com/13546486/55837042-8205a580-5b20-11e9-933b-d2b83768e6f2.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2353)

*   [@韵](https://dev.to/rhymes)重新生成了自动 Rubocop `TODO`文件，并沿途修复了`RSpec/FilePath` cop/rule。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Rubocop:重新生成 todo 并修复 RSpec/file path# 2358](https://github.com/thepracticaldev/dev.to/pull/2358)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-10T12:18:42Z">Apr 10, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2358)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

重新生成 todo 文件并修复 RSpec/FilePath

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2358)

*   [@venarius](https://dev.to/venarius) 修复了夜间模式下评论预览的问题。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UX:修正了黑暗模式下的评论预览 #2363](https://github.com/thepracticaldev/dev.to/pull/2363) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-10T16:26:55Z">Apr 10, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2363)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

这修复了黑暗模式下的注释预览

## 相关车票&单据

关闭#2286

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![55394990-e4c1d480-5555-11e9-818e-3740ee057274](img/e6bbf0295cbe0e940f09142032730d25.png)](https://user-images.githubusercontent.com/13546486/55896239-22f66e00-5bbe-11e9-93d2-d21c199c26bc.png)

### 后

[![Screenshot from 2019-04-10 18-23-09](img/c7f5893cd87f532048aeca834b4a07c6.png)](https://user-images.githubusercontent.com/13546486/55896247-2853b880-5bbe-11e9-8d71-72972bb5efdb.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2363)

*   [@韵](https://dev.to/rhymes)去掉了一个不用的`attr_reader`。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移除未使用的 attr _ reader# 2376](https://github.com/thepracticaldev/dev.to/pull/2376)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-11T15:09:39Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2376)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

在#2366 中已经删除了`request_id` `attr_reader`的用法

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2376)

*   [@rhymes](https://dev.to/rhymes) 通过改变我们查询已发布帖子的方式来重构查询。现在就像`Article.published`一样简单。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 使用发表文章范围 #2374](https://github.com/thepracticaldev/dev.to/pull/2374) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-11T11:51:25Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2374)

## 这是什么类型的公关？(勾选所有适用选项)

*   [X]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

`where(published: true)`是一个无所不在的条件，在代码中选择各种文章。专用的作用域使得打字更短，并且不言自明。

一些说明符被重新排序以遵循类似的`WHERE`查询结构(我对此并不太有条理，最终的顺序对 Rails 来说并不重要)

我还禁用了 erblint 的 rubocop `Layout/InitialIndentation`,因为它会引发很多误报

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2374)

*   [@维纳利斯](https://dev.to/venarius)修复了`<figcaption>`标签在夜间模式下的外观。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【小】UX:修正了 nightmode 中的 fig caption# 2383](https://github.com/thepracticaldev/dev.to/pull/2383)

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-11T17:15:33Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2383)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

修复了夜间模式下的图片标题标签

## 相关车票&单据

关闭#2181

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![Screenshot from 2019-04-11 19-11-46](img/7732e4d2a8b8aba571adafa2885589f8.png)](https://user-images.githubusercontent.com/13546486/55977043-1ccfc280-5c8e-11e9-96b4-3cbee12d1897.png)

### 后

[![Screenshot from 2019-04-11 19-13-06](img/0af9aa5e7633d6124d2924793af82e08.png) ](https://user-images.githubusercontent.com/13546486/55977049-20634980-5c8e-11e9-8133-61817f59efef.png) [ ![Screenshot from 2019-04-11 19-13-32](img/4695b97e52b95b9a729742d6086b0f41.png)](https://user-images.githubusercontent.com/13546486/55977054-21947680-5c8e-11e9-9814-74c9d1774b95.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2383)

*   [@venarius](https://dev.to/venarius) 还修复了用夜间模式搜索时“找不到结果”的部分。再次感谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【小】UX:修复了在 nightmode   #2384](https://github.com/thepracticaldev/dev.to/pull/2384) 中找不到结果

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-11T18:08:35Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2384)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

这修复了在夜间模式下搜索时“找不到结果”的问题

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![Screenshot from 2019-04-11 20-04-42](img/6595241c25d6e1366b303e6056ecab32.png)](https://user-images.githubusercontent.com/13546486/55980789-81dae680-5c95-11e9-9fb7-4529b5c39ff0.png)

### 后

[![Screenshot from 2019-04-11 20-05-29](img/b286998a046ec6a91de84ff53e6e5291.png) ](https://user-images.githubusercontent.com/13546486/55980797-86070400-5c95-11e9-8471-dc536fc6a7ac.png) [ ![Screenshot from 2019-04-11 20-05-53](img/fd414ca11fb3a69e34fc1376e4127c67.png)](https://user-images.githubusercontent.com/13546486/55980802-87383100-5c95-11e9-88db-6371d14e137c.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2384)

*   [@chinchang](https://dev.to/chinchang) 修复了“设置更新”消息的颜色对比。谢谢， [@chinchang](https://dev.to/chinchang) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  settings.scss:修复文本颜色中的颜色对比度 #2372](https://github.com/thepracticaldev/dev.to/pull/2372) 

[![chinchang avatar](img/ad3e2b4ae84b41e2a1dc2ee8b705d74b.png)](https://github.com/chinchang) **[chinchang](https://github.com/chinchang)** posted on [<time datetime="2019-04-11T10:59:45Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2372)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

此 PR 修复了警报消息的文本颜色，该颜色未能满足 4.5:1 比率的可访问颜色对比度要求。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

之前: [![Screenshot 2019-04-11 16 28 47](img/6dddb3e8972480cbe3af358d3500b049.png)](https://user-images.githubusercontent.com/379918/55952332-fbafa780-5c76-11e9-8814-c50bd2494c4f.png)

[![Screenshot 2019-04-11 16 29 03](img/e56363a4a3fa03431fec5492935181f7.png)](https://user-images.githubusercontent.com/379918/55952322-f5b9c680-5c76-11e9-9dc0-8178d92c91ad.png) 之后

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

CC @benhalpern

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2372)

*   @alissalikavec 修复了社区审核页面上的一个语法问题。谢谢@alissalikeavec！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 语法修正 #2390](https://github.com/thepracticaldev/dev.to/pull/2390) 

[![alissalikavec avatar](img/1ad447a1fcb2c8236b6abcad6673faf7.png)](https://github.com/alissalikavec) **[alissalikavec](https://github.com/alissalikavec)** posted on [<time datetime="2019-04-11T21:06:53Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2390)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

更新了语法，从“只有预告段落的文章，将读者发送到其余部分的外部链接”改为“只有预告段落的文章，将读者发送到其余部分的外部链接”:)

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![fuzzy hugs](img/b2ef3af098c1e255d7415ae352f1ba62.png)](https://camo.githubusercontent.com/48f64129d69a57734aaab1249efaaa3a0d935c3c/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f4a7a734730456d485939654b632f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2390)

*   [@lyonsbp](https://dev.to/lyonsbp) 修复了社区审核页面上的一个空格错别字。谢谢， [@lyonsbp](https://dev.to/lyonsbp) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)【2388 号](https://github.com/thepracticaldev/dev.to/pull/2388)

[![lyonsbp avatar](img/56dafc7f2b6b7f4465128e514557c88d.png)](https://github.com/lyonsbp) **[lyonsbp](https://github.com/lyonsbp)** posted on [<time datetime="2019-04-11T21:06:15Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2388)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

修复了文件`community_moderation.html.erb`中的一个打字错误

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2388)

*   [@yasmeen](https://dev.to/yasmeen) 还修复了社区审核页面的一些错别字。谢谢， [@yasmeen](https://dev.to/yasmeen) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复错别字 #2396](https://github.com/thepracticaldev/dev.to/pull/2396) 

[![yasmeen avatar](img/7fab865de70c620e4e4bd841f3d4f323.png)](https://github.com/yasmeen) **[yasmeen](https://github.com/yasmeen)** posted on [<time datetime="2019-04-11T22:04:08Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2396)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

在社区审核页面的“呕吐”描述中有几个错别字。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![cute puppy](img/82d35ffd960a455c1514964815cc11ff.png)](https://camo.githubusercontent.com/c9dff8786e607329370e6e265d509bc410e23c73/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f31326350584a33365558356e4f302f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2396)

*   [@韵脚](https://dev.to/rhymes)重构了一些查询，减少了 Rails 的`touch`。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 用# 2395](https://github.com/thepracticaldev/dev.to/pull/2395)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-11T21:57:32Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2395)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

`.touch()`向数据库发出一条 SQL `UPDATE`语句，但如果它与`update`或`save`相关，则不需要，因为它们都将发出一条 SQL `UPDATE`。

通过这种方式，我们节省了一些与数据库之间的往返。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2395)

*   [@ben](https://dev.to/ben) 在首页增加了额外的缓存。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 首页添加额外的片段缓存 #2401](https://github.com/thepracticaldev/dev.to/pull/2401) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-12T04:27:40Z">Apr 12, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2401)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这是快速和肮脏的，但应该使主页更有效一点

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2401)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 为首页缓存添加侧栏条件 #2404](https://github.com/thepracticaldev/dev.to/pull/2404) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-12T05:06:34Z">Apr 12, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2404)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2404)

*   @lightalloy 在评论表中添加了缺失的索引。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在备注表  #2413](https://github.com/thepracticaldev/dev.to/pull/2413) 中添加了缺失的指标

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-12T09:39:50Z">Apr 12, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2413)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]优化

## 描述

增加了对`comments` `commentable`领地和`user_id`的索引

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2413)

*   @venarius 修正了草稿在夜间模式下在你的仪表盘上的外观。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UX:在夜间模式下固定吃水 #2382](https://github.com/thepracticaldev/dev.to/pull/2382) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-11T17:09:09Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2382)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

这修正了夜间模式(仪表板)的草稿，也改变了视频 cta，现在看起来更像一个按钮

保持背景多一点灰色，这样用户可以很容易地看出这是一个草稿<g-emoji class="g-emoji" alias="slightly_smiling_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f642.png">🙂</g-emoji>

## 相关车票&单据

关闭#2182

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![Screenshot from 2019-04-11 18-50-02](img/ccb566e516d4f707f17595b67511e5cd.png)](https://user-images.githubusercontent.com/13546486/55976584-05dca080-5c8d-11e9-8c92-37a0d91b16ad.png)

### 后

[![Screenshot from 2019-04-11 19-03-34](img/621d02ed23d1ab750c6bba1e1918a0be.png) ](https://user-images.githubusercontent.com/13546486/55976591-0bd28180-5c8d-11e9-930f-024dfa2d90e5.png) [ ![Screenshot from 2019-04-11 19-03-15](img/65c06cfff0fcca732134cecb39c88e62.png)](https://user-images.githubusercontent.com/13546486/55976594-0d9c4500-5c8d-11e9-87af-2e230f6f93ae.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2382)

*   [@desi](https://dev.to/desi) 修复了版主“thumbsdown”描述中的一个语法错误。谢谢，[@德西](https://dev.to/desi)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复了《拇指镇》描述中的语法错误 #2391](https://github.com/thepracticaldev/dev.to/pull/2391) 

[![desirottman avatar](img/7a247594d3829a8ddf4907798c960169.png)](https://github.com/desirottman) **[desirottman](https://github.com/desirottman)** posted on [<time datetime="2019-04-11T21:21:05Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2391)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [X]错误修复
*   [ ]文档更新

## 描述

在 Thumbsdown 描述中有一个不正确的“”,所以我将其更改为匹配！

## 相关车票&单据

初始显示截图:[https://cl.ly/5e8a52d25344](https://cl.ly/5e8a52d25344)

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [X]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![proud gif!](img/6449f2a9dcb518a97ae05fe2458b8a76.png)](https://camo.githubusercontent.com/16d4eb058f1ade3474effc078b2c9e4e8519008e/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f707a4b4f58544c54313859424b6b5137336d2f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2391)

*   [@desi](https://dev.to/desi) 从目录中删除了我们的产品路线图链接。再次感谢， [@desi](https://dev.to/desi) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 从目录中删除产品路线图链接 #2423](https://github.com/thepracticaldev/dev.to/pull/2423) 

[![desirottman avatar](img/7a247594d3829a8ddf4907798c960169.png)](https://github.com/desirottman) **[desirottman](https://github.com/desirottman)** posted on [<time datetime="2019-04-13T02:43:58Z">Apr 13, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2423)

貌似在[https://github . com/thepractical dev/dev . to/commit/65 bdaa 86 F3 c 598 ab 95 e 557 a 92365642 f 34037 be 4 # diff-04c 6 e 90 faac 2675 aa 89 e 2176 D2 EEC 7d 8](https://github.com/thepracticaldev/dev.to/commit/65bdaa86f3c598ab95e557a92365642f34037be4#diff-04c6e90faac2675aa89e2176d2eec7d8)删除了 Read Me 的产品路线图部分，以至于 anchor 断了。这将从目录中删除链接。

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [X]文档更新

## 描述

从目录中删除了一个断开的链接

## 相关车票&单据

从 README 中删除的产品路线图位于[https://github . com/thepractical dev/dev . to/commit/65 bdaa 86 F3 c 598 ab 95 e 557 a 92365642 f 34037 be 4 # diff-04c 6 e 90 faac 2675 aa 89 e 2176 D2 EEC 7d 8](https://github.com/thepracticaldev/dev.to/commit/65bdaa86f3c598ab95e557a92365642f34037be4#diff-04c6e90faac2675aa89e2176d2eec7d8)

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [X] readme
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![wrestler Big E doing a lil' dance in a wrestling ring](img/2a25aa665d01627aefd53e993f53c5a8.png)](https://camo.githubusercontent.com/f993d24029eecd3f64ce89803958086669fa51fa/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f4f4e47393443635067424f754b4b5277566e2f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2423)

*   [@princesso](https://github.com/princesso) 修复了社区审核页面的一个错别字。谢谢你，公主！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复社区审核指南页面 #2394](https://github.com/thepracticaldev/dev.to/pull/2394) 

[![Princesso avatar](img/bb895d48a383c5dfbc3bfb25d453e082.png)](https://github.com/Princesso) **[Princesso](https://github.com/Princesso)** posted on [<time datetime="2019-04-11T21:48:35Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2394)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

此拉取请求中修复了两个语法错误。一个在 52 行，一个在 53 行。替换的文字是

*   有->有
*   拍摄->拍摄

## 相关车票&单据

community_moderation.html.erb

## 手机&桌面截图/录音(如有 UI 改动)

不适用

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2394)

*   [@lightalloy](https://dev.to/lightalloy) 把估计用户默认语言移到了`ActiveJob`。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将估算用户默认语言移动到活动工单 #2373](https://github.com/thepracticaldev/dev.to/pull/2373) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-11T11:24:10Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2373)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   将逻辑移到单独的类中
*   创建单独的作业
*   保留旧的`estimate_default_language_without_delay!`方法，以防现有作业使用它

## 相关车票&单据

准备修理#2368

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2373)

*   [@押韵](https://dev.to/rhymes)修复了我们如何使用`COMMUNITY_NAME`环境变量的问题。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修罗社区名称 #2415](https://github.com/thepracticaldev/dev.to/pull/2415) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-12T16:02:58Z">Apr 12, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2415)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这个 PR 修复了几个实例，其中`The #{ApplicationConfig['COMMUNITY_NAME']} Community`被逐字写入，而不是用实际文本替换。

我还介绍了一个名为`community_qualified_name`的应用助手，并提供了全文。

仅供参考:代码中有一些`#{ApplicationConfig['COMMUNITY_NAME']} Community`的实例没有`The`，还有一些`PracticalDev`或`DEV`的实例或其他变体。

## 相关车票&单据

关闭#2414

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2415)

*   [@dcrow](https://github.com/dcrow) 修复了表情符号在代码块中被解析的问题。谢谢， [@dcrow](https://dev.to/dcrow) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复代码块中的冒号表情符号(#2216)  #2360](https://github.com/thepracticaldev/dev.to/pull/2360) 

[![DCrow avatar](img/b4919329d0d312b41c5a00f313174885.png)](https://github.com/DCrow) **[DCrow](https://github.com/DCrow)** posted on [<time datetime="2019-04-10T13:30:05Z">Apr 10, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2360)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [X]错误修复
*   [ ]文档更新

## 描述

通过将`code`标签及其内容排除在`EmojiConverter`处理之外，修复了该缺陷

### 片段使用

```
:o: 
```

```
'⭕'
```

Enter fullscreen mode Exit fullscreen mode

```
'⭕'
"⭕"
```

Enter fullscreen mode Exit fullscreen mode

`:o:`

### 以前

[![Screen Shot 2019-04-10 at 16 24 26](img/6d626ed4112233b88d78c35fcf65b237.png)](https://user-images.githubusercontent.com/8961745/55882436-377e3a80-5bad-11e9-9f2c-cf2b7b74c76a.png)

### 后

[![Screen Shot 2019-04-10 at 16 23 52](img/1a7b7bd1f1469ec094623af641a5d357.png)](https://user-images.githubusercontent.com/8961745/55882432-34834a00-5bad-11e9-8333-e2cecc088b74.png)

## 相关车票&单据

修复#2216

## 手机&桌面截图/录音(如有 UI 改动)

没有用户界面更改

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [X]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2360)

*   [@venarius](https://dev.to/venarius) 对 Connect 在夜间模式下的外观做了几处改进。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UX:对 nightmode   #2430](https://github.com/thepracticaldev/dev.to/pull/2430) 聊天的几点改进

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-13T21:55:35Z">Apr 13, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2430)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

对夜间聊天模式的一些改进

此外，哈士奇已经 linted 了一些反应文件，我作出了改变。

## 相关车票&单据

关闭#2417

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![56056991-c1eca880-5d2b-11e9-8b37-25631c57ca4c](img/f5f6a348cf3a486ac525e553f8b8c7dc.png)](https://user-images.githubusercontent.com/13546486/56085663-3a945780-5e47-11e9-9ac8-d7bed7da0b7d.png)

[![56057006-cc0ea700-5d2b-11e9-9705-31d755d8da81](img/8521abda8f5b9ed6ab79de6ba518f549.png)](https://user-images.githubusercontent.com/13546486/56085664-3d8f4800-5e47-11e9-9ff6-53629bf1132d.png)

[![56057286-7090e900-5d2c-11e9-95d4-c7e6a11111ba](img/1053dccf5f8a8f82fcae348c51834af3.png)](https://user-images.githubusercontent.com/13546486/56085665-3ec07500-5e47-11e9-93fc-a437654937c6.png)

### 后

[![Screenshot from 2019-04-13 22-56-42](img/ded268d5225d921b570dfcdf505d663d.png)](https://user-images.githubusercontent.com/13546486/56085668-4aac3700-5e47-11e9-9e65-bace0e33fc80.png)[![Screenshot from 2019-04-13 23-43-56](img/54acb1246a3797f2f3ff85a3f0bf33a1.png)](https://user-images.githubusercontent.com/13546486/56085669-4bdd6400-5e47-11e9-9d29-9d6910ae8ecc.png)[![Screenshot from 2019-04-13 23-24-04](img/38df35635eebe0fd8af059730a7742d1.png)](https://user-images.githubusercontent.com/13546486/56085670-4d0e9100-5e47-11e9-8ed4-96cbc896a2cf.png)[![Screenshot from 2019-04-13 23-44-11](img/9bd1b36d574506be2a0f42f40c654c2a.png)](https://user-images.githubusercontent.com/13546486/56085671-4ed85480-5e47-11e9-819f-91b704c3dcd7.png)[![Screenshot from 2019-04-13 23-42-44](img/6b5fc73b2af66876dbf3b39ca1631318.png)](https://user-images.githubusercontent.com/13546486/56085672-50098180-5e47-11e9-8734-f2746e495c90.png)[![Screenshot from 2019-04-13 23-44-27](img/fde8935d74d851ae305834f9a5951650.png)](https://user-images.githubusercontent.com/13546486/56085673-513aae80-5e47-11e9-8cb1-59634c962e42.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![1488e4dd21193905a6c145e20efa24f5](img/6b22173001f0fa7eb281e43bf5811e29.png)](https://user-images.githubusercontent.com/13546486/56085680-6b748c80-5e47-11e9-906e-69fce48c6687.gif)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2430)

*   [@venarius](https://dev.to/venarius) 修正了开发者徽章在夜间模式下的外观。再次感谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【小】UX:修正了夜行模式下的开发徽章 #2431](https://github.com/thepracticaldev/dev.to/pull/2431) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-13T22:26:59Z">Apr 13, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2431)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这将修复夜间模式下配置文件设置中的开发徽章

## 相关车票&单据

关闭#2420

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![56062287-ddf74680-5d39-11e9-828e-db97b6a366b6](img/47c28b0c4c91d10366fe2c61a00257c0.png)](https://user-images.githubusercontent.com/13546486/56085900-f5beef80-5e4b-11e9-95c3-c60877b2c1d2.png)

### 后

[![Screenshot from 2019-04-14 00-24-45](img/bc792e3d100a360e62ee6c0992d21f27.png)](https://user-images.githubusercontent.com/13546486/56085901-f9527680-5e4b-11e9-883c-afd36f93dd14.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2431)

*   添加了对我们使用的红宝石的简短描述。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 为宝石  #2447](https://github.com/thepracticaldev/dev.to/pull/2447) 添加简短描述

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-15T13:49:35Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2447)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

该应用程序使用了许多 gem，并不总是清楚哪个做什么，我给每个都添加了一个简短的描述(取自 RubyGems 页面或 GitHub 页面)。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2447)

*   [@押韵](https://dev.to/rhymes)移除了部分未使用的宝石:`csv_shaper`、`bourbon`、`share_meow_client`。谢谢，[@押韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移除宝石 csv_shaper，波旁和 share _ 喵 _client  #2450](https://github.com/thepracticaldev/dev.to/pull/2450) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-15T14:28:07Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2450)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

无论是 [csv_shaper](https://rubygems.org/gems/csv_shaper) ，还是 [bourbon](https://rubygems.org/gems/bourbon) ，还是 [share_meow_client](https://rubygems.org/gems/share_meow_client) 似乎都没有在代码中的任何地方使用。

我还删除了 Share Meow 服务的 env 变量

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2450)

*   我解决了一个小错误，GitHub readmes 中的空白链接不能被正确解析。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加一个空白的 href 属性如果空白则为 GitHub readme 标签 #2457](https://github.com/thepracticaldev/dev.to/pull/2457) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-04-15T16:52:07Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2457)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

这解决了#2452，其中一些存储库的 READMEs 包括没有`href`属性的`<a>`标签。比如:`<a name="anchor-header"></a>`

修复添加了`href`属性，并将其设置为空字符串。我们用下面的代码行修改了链接的值，以链接回原始存储库，所以我认为这是一个相当安全的修复。

[https://github . com/thepractical dev/dev . to/blob/e 389757 aa 4 ECF 23 F2 b 788 F8 c2a 07150 b 43005 a 35/app/liquid _ tags/github _ tag/github _ readme _ tag . Rb # L52](https://github.com/thepracticaldev/dev.to/blob/e389757aa4ecf23f2b788f8c2a07150b43005a35/app/liquid_tags/github_tag/github_readme_tag.rb#L52)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2457)

*   [@ben](https://dev.to/ben) 解决了一个小 bug 让`seo_boostable`更好的处理`nil`。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 允许 seo_boostable 更好的处理 nil  #2458](https://github.com/thepracticaldev/dev.to/pull/2458) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-15T16:58:32Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2458)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

目前，此方法的第二个参数中的 nil 值将导致一个空数组，而它应该导致默认值。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2458)

*   [@venarius](https://dev.to/venarius) 修复了 Twitter 嵌入/液体标签在夜间模式下的外观。感谢[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【小】UX:推特液态标签 Nightmode  #2460](https://github.com/thepracticaldev/dev.to/pull/2460) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-15T17:27:18Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2460)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

这修复了夜间模式下的 twitter 液体标签

## 相关车票&单据

关闭#2435

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![Screenshot from 2019-04-15 19-25-02](img/85a360d8c35fb0e32e85e84f82ab4fb4.png)](https://user-images.githubusercontent.com/13546486/56152593-6a14a280-5fb4-11e9-80be-b7ba545e7930.png)

### 后

[![Screenshot from 2019-04-15 19-24-50](img/97e559227f21be06b7af8c66b7505e16.png)](https://user-images.githubusercontent.com/13546486/56152597-6ed95680-5fb4-11e9-87d7-4e430d1a6a1b.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2460)

*   当我们获取谷歌分析时，@ben 增加了一些速率限制。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加 GA_FETCH_RATE ENV 变量缓解速率限制 #2461](https://github.com/thepracticaldev/dev.to/pull/2461) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-15T17:48:12Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2461)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这使我们能够设置从谷歌分析中获取的速率，以确保我们有效地处理速率限制。也许不是长期的解决办法，但解决目前的问题。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2461)

*   [@venarius](https://dev.to/venarius) 在夜间模式更新了很多其他的嵌入/液体标签。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UX:液态标签 Nightmode  #2459](https://github.com/thepracticaldev/dev.to/pull/2459) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-15T17:17:58Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2459)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这将在夜间模式下固定液体标签。

我遇到的一些问题:

*   我无法再现关于 javascript 标签 [@mariocsee](https://dev.to/mariocsee) 报道的问题(见下图)。起初我以为是`html_sanitizer`从`javascript`那里剥离了`script`部分，从而创建了一个非结束标签，但这不是问题所在。

*   标签的边框颜色由`app/liquid_tags/tag_tag.rb`中的以下代码生成

```
def dark_color
  HexComparer.new([@tag.bg_color_hex || "#0000000", @tag.text_color_hex || "#ffffff"]).brightness(0.88)
end 
```

lib 似乎没有办法获取当前用户和他使用的主题，所以我不能相应地修改这些值。这就是为什么我不得不在`variables.scss`中改变它。如果有人知道一个更好的处理这一点，我非常欢迎听到它。

## 相关车票&单据

#2397

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![55996122-7b903e80-5c84-11e9-8694-70421035366c](img/21204a6006a07a94255d20e356f8ffe3.png)](https://user-images.githubusercontent.com/13546486/56152021-ead29f00-5fb2-11e9-86fe-932b19516b00.png)

### 后

[![Screenshot from 2019-04-15 19-11-03](img/5b66400eda4c7879d6d733435a2997cd.png)](https://user-images.githubusercontent.com/13546486/56152034-f0c88000-5fb2-11e9-8f4a-7e48b4066867.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2459)

*   [@rhymes](https://dev.to/rhymes) 移除了一个 JQuery 插件`jquery.highlighttextarea`，因为它没有被使用。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除 jquery . highlight textarea# 2462](https://github.com/thepracticaldev/dev.to/pull/2462)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-15T19:52:39Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2462)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

我注意到在前端页面(除了内部页面之外)仍然有一些使用 jQuery 的代码，如果需要处理，这是一个单独的 PR，但当我环顾四周时，我注意到这两个文件在任何地方都没有使用。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2462)

*   [@韵](https://dev.to/rhymes)收拾了我们的`Gemfile`。查看我们的 PR 了解更多详情。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 清理一点 Gemfile  #2463](https://github.com/thepracticaldev/dev.to/pull/2463) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-15T20:44:50Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2463)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

我应用了以下更改:

*   pry 以前在生产中启用，现在只在开发和测试中可用
*   删除了未明确使用的宝石(r b-f event 和 sinatra)
*   在适当的组中移动了一些 gem(fix-d b-schema-conflicts，erb_lint 和 memory_profile 将只在开发阶段可用，vcr 只在测试阶段需要)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2463)

*   [@韵脚](https://dev.to/rhymes)将我们的无头测试浏览器从`chromedriver-helper`切换到`webdrivers`。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 从 from 驱动助手切换到 web 驱动 #2465](https://github.com/thepracticaldev/dev.to/pull/2465) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-16T10:05:44Z">Apr 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2465)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

[chromedriver-helper](https://github.com/flavorjones/chromedriver-helper) 已于 2019 年 3 月底正式弃用，其知识库目前已存档。创建者的建议是用更新的和维护过的 [webdrivers](https://github.com/titusfortner/webdrivers) 来替换它(它需要 selenium-webdriver 并自己打补丁)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2465)

*   [@venarius](https://dev.to/venarius) 修复了评论预览加载部分在夜间模式下的样子。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【小】UX:修复评论预览在夜间模式加载 #2454](https://github.com/thepracticaldev/dev.to/pull/2454) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-15T15:51:50Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2454)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这修复了在夜间模式下的评论预览加载动画

## 相关车票&单据

关闭#2359

## 手机&桌面截图/录音(如有 UI 改动)

### 以前

[![Screenshot from 2019-04-15 17-40-58](img/8931190ddaf05a653a40ed9980dd6338.png)](https://user-images.githubusercontent.com/13546486/56146416-75f96800-5fa6-11e9-9c68-62316efc6e48.png)

### 后

[![Screenshot from 2019-04-15 17-49-06](img/f386f7a5097de03440b10c23bebb2f31.png) ](https://user-images.githubusercontent.com/13546486/56146744-0df75180-5fa7-11e9-9cd9-ef27f66007fe.png) [ ![Screenshot from 2019-04-15 17-43-54](img/bc72f9dae82fea8f6af432f2011b3650.png)](https://user-images.githubusercontent.com/13546486/56146432-7bef4900-5fa6-11e9-82c3-2044808b9a62.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2454)

*   为了更好地跟踪错误，[@韵脚](https://dev.to/rhymes)将导出你的文章移到了`ActiveJob`中。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移动导出成作业 #2299](https://github.com/thepracticaldev/dev.to/pull/2299) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-03T17:29:07Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2299)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

我已经将导出器逻辑转移到一个适当的活动工作中，就像“t0”@ light alloy “t1”为其余的异步逻辑所做的那样。

这也有助于理解/规避出口请求。我已经等了一个多星期的导出，我怀疑这与使用`.delay`创建的作业的默认队列延迟作业有关。

## 相关车票&单据

这可能会给 https://github.com/thepracticaldev/dev.to/issues/2031 带来一些启示

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2299)

*   [@venarius](https://dev.to/venarius) 为我们所有的`.jsx`相关文件解决了一堆漂亮的(一个 JavaScript 代码格式化程序)问题。谢谢，[@维纳利斯](https://dev.to/venarius)！

# ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 参考:反应斯洛文尼亚语 #2432 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-14T00:24:35Z">Apr 14, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2432)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

因为大多数*。jsx 文件似乎没有被重构，当改变这些文件中的一行时，husky 会自动重构它们，我想“为什么不现在就重构它们呢？”

现在，漂亮女孩似乎对一切都很满意。jsx 文件。Eslint 仍有大约 250 个错误需要报告，但这些错误无法自动修复。因此，如果不使用`--no-verify`标志，当前也不可能提交对 jsx 文件的更改。

我们应该禁用其中一些规则，或者尝试修复所有 250 个错误。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2432)

*   我解决了一个帖子标题中下划线不能正确转换的小错误。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 正确转换文章中的下划线 #2472](https://github.com/thepracticaldev/dev.to/pull/2472) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-04-17T16:05:49Z">Apr 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2472)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

解决#2418

这将把带下划线的文章标题转换成有效的 slugs，因为 Rails' `parameterize`不幸地不处理下划线。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2472)

*   [@nickytonline](https://dev.to/nickytonline) 删除了“如何用 Windows/Linux/Ubuntu/not macOS 设置 repo？”部分。谢谢， [@nickytonline](https://dev.to/nickytonline) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除“我如何用 Windows/Linux/Ubuntu/非 macOS 设置回购？” #2480](https://github.com/thepracticaldev/dev.to/pull/2480) 

[![nickytonline avatar](img/849ad90a82f294a93718c2c4da688f82.png)](https://github.com/nickytonline) **[nickytonline](https://github.com/nickytonline)** posted on [<time datetime="2019-04-18T03:31:11Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2480)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

这部分文档可以删除，因为我们现在有 Linux、Windows 和 Mac 的安装文档。参见[https://docs.dev.to/installation/](https://docs.dev.to/installation/)

[![image](img/74d2b95dccb49beacc221141a7c979fa.png)](https://user-images.githubusercontent.com/833231/56334981-9ffa8800-6168-11e9-9539-19493ec4cfe9.png)

## 添加到文档中？

*   [x]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![Dave Chapelle Show Delete GIF](img/a086fecf607ecf5f9ccf8fb81e064b11.png)](https://camo.githubusercontent.com/44153b08d1bca9c23e2d816be3fa9740ea597058/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f776b4b526f374e3054314f4e4f2f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2480)

*   [@calebmer](https://dev.to/calebmer) 修复了网站页脚的`<br/>`标签。谢谢@名人！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在页脚处固定< br/ >。 #2491](https://github.com/thepracticaldev/dev.to/pull/2491) 

[![calebmer avatar](img/10da0936e1d754fd2ceaeb54acefea2f.png)](https://github.com/calebmer) **[calebmer](https://github.com/calebmer)** posted on [<time datetime="2019-04-18T19:36:28Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2491)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [X]错误修复
*   [ ]文档更新

## 手机&桌面截图/录音(如有 UI 改动)

当前行为:

[![image](img/5bf4a7be3307da1c05094e0110efa0cf.png)](https://user-images.githubusercontent.com/8282507/56386395-8001ad80-61d6-11e9-8be0-6cfaead13a78.png)

(我没有在本地测试)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2491)

*   [@nickytonline](https://dev.to/nickytonline) 解决了一些前端林挺问题。谢谢， [@nickytonline](https://dev.to/nickytonline) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复了一些前端林挺问题 #2495](https://github.com/thepracticaldev/dev.to/pull/2495) 

[![nickytonline avatar](img/849ad90a82f294a93718c2c4da688f82.png)](https://github.com/nickytonline) **[nickytonline](https://github.com/nickytonline)** posted on [<time datetime="2019-04-19T05:26:26Z">Apr 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2495)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

修正了一些在前端被报告的林挺问题。

## 相关车票&单据

#1828

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![Richie Rich robot](img/2a3d018a2b2bbb355fdde8a02f2d320e.png)](https://camo.githubusercontent.com/fe6511bf883730e6174796c40682c329c3ea635f/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f79777670647063676b3045614d65397a57772f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2495)

# 新的问题和讨论

*   @rhymes 开了一个极好的问题，讨论重构分析的几种选择。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 分析服务:未来可能的改进和想法 #2311](https://github.com/thepracticaldev/dev.to/issues/2311) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-05T09:13:29Z">Apr 05, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2311)

**您的功能请求是否与某个问题相关？请描述一下。**

在#2308 中的重构之前，在#2307 中提出了关于性能的问题之后，在需要的情况下，这已被打开以收集所有可能的分析 API 改进(对于导致#2307 的用例，AFAIK 尚未被测量/验证)。

**描述您想要的解决方案**

有可能的增量解决方案(不是所有的相互替代，有些可以增量应用)。一些建议的解决方案需要少量的工作，其他的需要更多的工作。

理论上，所有这些都应该根据现状来衡量，以了解是否需要。

# 可能的解决方案

## 硬性限制可以请求的数据分析的日期范围

提议的解决方案将强制限制客户可以请求的分析日期范围。正如@Zhao-Andy 所提到的，一些公共 API 如 Twitter 将请求限制在 30 天以内。

### 利弊

*   出于显而易见的原因，对客户端可以请求的数据量设置上限是一个好主意
*   大多数分析客户无论如何都会使用日期滚动窗口，或者对特定时间段感兴趣

### 弊

*   如果任何客户被区别对待，可能需要特别小心(对于客户要求同时访问更多数据的商业合作关系)
*   这可能会导致对更大范围感兴趣的客户端发出更多的 HTTP 请求，因此，如果客户端需要的数据超过 30 天，就会有更多的查询

### 我的推荐

我认为这是一个很好的想法，当然应该加以考虑，尽管这是一个清晰的数据策略的结果(甚至可以在文档中明确解释),而不仅仅是因为性能原因(如果遇到这些问题，应该尽我们所能解决，而不仅仅是通过限制日期范围来“隐藏”)

## 向 where 条件中的列添加索引

`AnalyticsService`中的大多数查询都是在表`reactions`、`comments`、`page_views`的`created_at`列上进行的。建议的解决方案是在这些列上添加索引，以提高大型数据集的检索速度。

### 利弊

*   在一个很长的时间间隔内要求大量文章的请求应该有索引的帮助

### 弊

*   对于写入频率高的表，索引会降低写入速度
*   按照目前的情况，这段代码将每天的查询分开，在大多数情况下，这种差异可能并不明显

### 我的推荐

我认为这是一个立竿见影的方法，应该认真考虑作为其他解决方案的补充。其中日期条件是指数受益的典型地方。理想情况下，可以打开 SQL 控制台，用解释分析来测量查询的前后，但这不是强制性的。我也不相信，AFAIK，会对写入有任何影响，因为我认为是三个表中最大的表，`reactions`，不会一次得到几百万个`INSERT`。无论如何，可以通过 Heroku 上的慢速查询选项卡进行监控，以防将来出现问题。

## 使用分组

建议的解决方案是让数据库对数据进行分组并返回聚合，而不是在日期范围内每天执行以下查询来计算聚合。

### 利弊

*   数据库通常非常擅长混合分组和 where 条件(假设在需要的列上有索引)
*   这将减少查询的数量

### 弊

*   这需要测试。理想情况下，应该编写一个测试，在 2 到 3 天内用数据样本填充数据库，编写一个关于结果的断言/期望，然后着手改进服务的内部结构，以确保期望的数据与新的查询结构相匹配(通常的测试，这里没什么可看的，呵呵)
*   这需要一些反复试验和对 PostgreSQL group by 子句的知识(链接指向一个教程)

### 我的推荐

如果预期结果允许的话(我没有深入研究过，所以可能不可行)，我显然倾向于这样做(让数据库像咒语一样完成它的工作)。Rails 通过 ActiveRecord 方法 [group](https://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html#method-i-group) 和 [having](https://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html#method-i-having) 原生支持`GROUP BY`和`HAVING`(这两个子句都需要分组和分组过滤)。 [@lightalloy](https://dev.to/lightalloy) 提到了 gem [groupdate](https://github.com/ankane/groupdate) 的存在，它专门针对使用在所有受支持的数据库上工作的不可知接口的日期分组，这可以方便地用于基于日期的聚合(尽管 vanilla Rails 中的语法非常简单:

```
[7] pry(main)> PageView.where(created_at: 2.days.ago..Time.current).group("date(created_at)").sum(:counts_for_number_of_views)
   (0.8ms)  SELECT SUM("page_views"."counts_for_number_of_views") AS sum_counts_for_number_of_views, date(created_at) AS date_created_at FROM "page_views" WHERE "page_views"."created_at" BETWEEN $1 AND $2 GROUP BY date(created_at)  [["created_at", "2019-04-03 08:47:39.869415"], ["created_at", "2019-04-05 08:47:39.869509"]] [sql_query]
=> {Thu, 04 Apr 2019=>7}
```

Enter fullscreen mode Exit fullscreen mode

您可以通过快速查看数据来验证查询如何正确工作:

```
[8] pry(main)> PageView.all.pluck(:counts_for_number_of_views, :created_at)
   (0.5ms)  SELECT "page_views"."counts_for_number_of_views", "page_views"."created_at" FROM "page_views" [sql_query]
=> [[1, Thu, 04 Apr 2019 21:28:07 UTC +00:00],
 [1, Thu, 04 Apr 2019 21:28:56 UTC +00:00],
 [1, Thu, 04 Apr 2019 21:39:25 UTC +00:00],
 [1, Thu, 04 Apr 2019 22:39:33 UTC +00:00],
 [1, Thu, 04 Apr 2019 22:41:03 UTC +00:00],
 [1, Thu, 04 Apr 2019 22:43:49 UTC +00:00],
 [1, Thu, 04 Apr 2019 22:45:07 UTC +00:00]]
```

Enter fullscreen mode Exit fullscreen mode

## 使用批处理预先计算数据，并实时计算差异

建议的解决方案是添加一个分析表，其中包含由计划的重复作业填充的聚合数据

### 职业选手

*   这显然有利于获得接近瞬时的读取时间。客户机请求一个日期范围，向聚合表发出一个`SELECT`,然后返回数据
*   它可以带来进一步的优化(如果与缓存或其他我现在无法预见的不断变化的业务需求相结合)

### 弊

*   这需要前期的一点努力
*   它需要一个合并函数(如果聚集的数据少于所需的数据),该函数仍然必须转到活动表中
*   它可能需要驱逐策略(聚合在存储中保留多长时间？)
*   如果分析功能中存在缺陷或者添加了新的指标，旧的聚合数据行将会发生什么情况？

### 我的推荐

当其他选择都用尽时，我会考虑这个。如果 HTTP 客户端不断请求大量数据，而索引和分组却无法满足您的需求，那么方法上的一个转变是一个不错的选择。我总是很不情愿“先批处理”,因为大多数情况下有很多方法可以尝试。批处理还需要一些关于合并、驱逐和处理陈旧数据的逻辑(或者必须在后台重新计算旧的行)

## 对结果进行低级缓存

建议的解决方案是用缓存保护一个或每个指标，这样，两次请求特定范围的单个客户端将在第二次从内存中返回其数据

### 利弊

*   与批处理一样，一旦启动缓存，数据可以快速返回
*   通过预先知道，可以预先计算特定用户/组织的缓存值，这些用户/组织前面有特别大量的计算
*   它应该不会对缓存服务器占用的内存产生很大的影响

### 弊

*   组织不太可能会多次请求相同的日期范围，因此缓存可能会违背目的(组织通常编写计划脚本来检索分析和更新内部仪表板:D)
*   缓存单个日期需要多次访问缓存服务器，在这种情况下，SQL 查询可能会更快
*   与批处理(基本上是将缓存写入磁盘)一样，如果明天添加了一个新的指标，就需要有一种机制来重新填充旧的缓存或至少清空所有现有的缓存，这需要进行一些分析

### 我的推荐

在穷尽了所有“实时优化”之后，我会走这条路，尽管它对更大的组织或非常受欢迎的用户或以连续方式请求分析的组织/用户非常有益，尽管通常每个客户端每天都会请求一次聚合分析，因此很容易规划对系统的影响。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2311)

*   [@coreyja](https://dev.to/coreyja) 开了一个关于自助直播的讨论。谢谢， [@coreyja](https://dev.to/coreyja) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)# 2317](https://github.com/thepracticaldev/dev.to/issues/2317)

[![coreyja avatar](img/dbdff6da4be51f9ef3a82a4163b7e2b0.png)](https://github.com/coreyja) **[coreyja](https://github.com/coreyja)** posted on [<time datetime="2019-04-06T00:27:18Z">Apr 06, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2317)

在@ benhalpern[https://dev . to/dev team/dev-feature-idea-self-serve-live-broadcasting-5 bli](https://dev.to/devteam/dev-feature-idea-self-serve-live-broadcasting-5bli)的这篇文章之后，我把这里作为一个讨论的地方

我想公开讨论这个问题，并弄清楚在要构建的开发中可能会涉及哪些特性。我也愿意在这个项目上工作，并为此做出贡献，这似乎是一个好的开始！

**您的功能请求是否与某个问题相关？请描述一下。**我以前尝试过几次直播编码，但感觉从来没有一个好的平台适合开发者关注的直播。

**描述您想要的解决方案**我认为最好的开始方式可能是嵌入一个或多个流媒体平台，如 Twitch 和 Youtube Live。最初帖子中的大多数评论者似乎认为嵌入是一个好的起点。

这就提出了一个问题，如何处理 Ben 在原始帖子中提到的聊天，即 dev.to 有一些内部聊天架构构建。这是一个潜在的解决方案，但意味着每个流可能需要管理两个聊天。主要的流平台 chat 和 dev.to chat 另一个解决方案是完全嵌入一个流平台，包括 chat。Twitch(1)和 Youtube Live (2)都支持这种行为。这种方法的一个缺点是，它使得开发社区更难直接调节。

restream.io 确实提供了一些聊天整合工具，但我不太了解他们的产品或如何与他们集成。

**描述您考虑过的替代方案**一个替代方案是原生托管直播平台。我承认我在这个领域没有太多的经验，但是我觉得这是一个需要构建和维护的大量工作，这与当前的开发产品和(未受教育的)假定基础设施无关。然而，原生版本可能会使聊天问题变得更容易，因为拥有原生聊天功能已经是一个巨大的优势了！

(1)[https://dev.twitch.tv/docs/embed/everything/](https://dev.twitch.tv/docs/embed/everything/)(2)[https://support.google.com/youtube/answer/2524549?hl=en](https://support.google.com/youtube/answer/2524549?hl=en)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2317)

*   [@vnbrs](https://dev.to/vnbrs) 请求一个关于能够查询和可视化 dev.to 的数据的特性。谢谢，@vnbrs！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 有什么方法可以可视化 dev.to 的数据？ #2320](https://github.com/thepracticaldev/dev.to/issues/2320) 

[![vnbrs avatar](img/f04e4c6c2b846107b3b897730697bcc4.png)](https://github.com/vnbrs) **[vnbrs](https://github.com/vnbrs)** posted on [<time datetime="2019-04-07T13:09:24Z">Apr 07, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2320)

如果有一种方法可以公开查询 dev.to 数据就好了，就像[Stack exchange Data Explorer](https://data.stackexchange.com/stackoverflow/query/new)，用户可以在 Stack Overflow 数据库上运行 SQL 查询(显然是一个克隆)。

可以建立的解决方案的数量是无限的:每月用户增长，标签趋势，顶级评论者，顶级海报，活跃用户，顶级组织，等等。

为了避免人们滥用这一功能，可以像 StackExchange 一样完成，它只对可信用户可用(基于他们的“分数”)。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2320)

*   [@kleinfreund](https://dev.to/kleinfreund) 打开了一个允许标签具有大写字符或分隔符的可访问性问题。谢谢， [@kleinfreund](https://dev.to/kleinfreund) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 可访问性:允许标签被破折号分隔或者允许它们包含大写字母 #2347](https://github.com/thepracticaldev/dev.to/issues/2347) 

[![kleinfreund avatar](img/429644d3106562d89ac267d66098722e.png)](https://github.com/kleinfreund) **[kleinfreund](https://github.com/kleinfreund)** posted on [<time datetime="2019-04-09T05:56:33Z">Apr 09, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2347)

**您的功能请求是否与某个问题相关？请描述一下。**是的。目前，据我所知，dev.to 上的标签是小写的，可能包含字母数字字符(或多或少)。例如，不允许使用以下标签:

*   `#custom-properties`
*   `#CustomProperties`
*   `#custom_properties`

这使得标签变得不必要的难以阅读，尤其是对于那些一开始就觉得难以阅读或者刚刚学会阅读英语的用户群。

**描述你想要的解决方案**最好，标签允许破折号(`-`)作为分隔符。下划线不理想，因为它可能会被下划线遮住。

**描述你考虑过的备选方案**或者，字符允许大写，这将允许用户写标题大小写喜欢(作为参考，见 Twitter，这是可能的)。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2347)

*   [@andrewbrown](https://dev.to/andrewbrown) 请求了一项功能，您可以通过草稿、已发布的帖子或所有帖子对仪表板进行排序。谢谢， [@andrewbrown](https://dev.to/andrewbrown) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 通过仪表盘可以按“所有帖子”、“草稿”和“已发表” #2351](https://github.com/thepracticaldev/dev.to/issues/2351) 

[![omenking avatar](img/c5b3096d42a99b0bcad72567d37cd4a0.png)](https://github.com/omenking) **[omenking](https://github.com/omenking)** posted on [<time datetime="2019-04-09T19:44:33Z">Apr 09, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2351)

**您的功能请求是否与某个问题相关？请描述一下。对于内容创建者来说，能够过滤所有的文章、草稿和已发表的文章是件好事。**

**描述你想要的解决方案**一个下拉菜单，默认显示所有文章，包括草稿和已发布的文章。

**描述你考虑过的备选方案**比起下拉列表，我更希望有一个草稿和已发布之间的切换，没有“所有帖子”过滤器。我建议这样做的原因是，我们根据上下文来定位这些列表，使其具有独特的功能。

因此，如果列表中的已发布文章不共享相同的模板部分，我们可以对已发布列表的附加信息一目了然。一个独特的发布列表的这种附加的未来特征的野生例子可以是文章观看/反应的跑马灯图。

**附加语境**无。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2351)

*   [@drawcard](https://dev.to/drawcard) 报告了一个问题，当光标悬停在提要中一篇文章的边框上时会闪烁。谢谢， [@drawcard](https://dev.to/drawcard) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 光标悬停在边框上时闪烁 #2355](https://github.com/thepracticaldev/dev.to/issues/2355) 

[![drawcard avatar](img/5c8c84516ccf8db74f1e7944cf9f7f6b.png)](https://github.com/drawcard) **[drawcard](https://github.com/drawcard)** posted on [<time datetime="2019-04-10T00:03:07Z">Apr 10, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2355)

**描述 bug** 当悬停在首页卡片边框上时，鼠标光标在常规光标和手形指针光标之间快速变换。

**重现**

*   参观[https://dev.to/](https://dev.to/)
*   将鼠标悬停在下面屏幕记录中描述的区域上。

**预期行为**光标不应闪烁，应默认为指针箭头或指针指针。

**截图**【https://gfycat.com/EverlastingOfficialArctichare】T2

**桌面(请填写以下信息):**

*   操作系统:macOS Mojave 10.14.3
*   浏览器:铬
*   版本:73.0.3683.86(官方版本)(64 位)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2355)

*   [@andrewbrown](https://dev.to/andrewbrown) 打开了一个开发者体验问题，请求更多种子数据。谢谢， [@andrewbrown](https://dev.to/andrewbrown) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)🌱更多种子数据 #2356](https://github.com/thepracticaldev/dev.to/issues/2356) 

[![omenking avatar](img/c5b3096d42a99b0bcad72567d37cd4a0.png)](https://github.com/omenking) **[omenking](https://github.com/omenking)** posted on [<time datetime="2019-04-10T00:12:23Z">Apr 10, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2356)

**您的功能请求是否与某个问题相关？请描述一下。**

处理此票证时，我没有足够的种子数据。[https://github.com/thepracticaldev/dev.to/issues/258](https://github.com/thepracticaldev/dev.to/issues/258)

**描述您想要的解决方案**

*   我将带来更多的标签
*   如果种子数据中不存在组织，我将生成一些假组织
*   我将在随机用户之外创建一个新用户。
*   对于这位新用户，我将随机生成数据，以便他们的控制面板中有更多数据。
*   我将添加一个端点，该端点将只在开发中工作，以便作为该用户轻松登录

**描述你考虑过的替代方案**没有。

**附加语境**无。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2356)

*   @fluffy 请求了一个功能来添加一个代词字段到个人资料。谢谢，[@毛毛](https://dev.to/fluffy)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 简介上的代词字段【2365 号](https://github.com/thepracticaldev/dev.to/issues/2365)

[![fluffy-critter avatar](img/1e7102b58be6e3d10514b752f07bbe17.png)](https://github.com/fluffy-critter) **[fluffy-critter](https://github.com/fluffy-critter)** posted on [<time datetime="2019-04-10T20:43:10Z">Apr 10, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2365)

**您的功能请求是否与某个问题相关？请描述一下。如果我们的用户资料可以包含代词就好了。**

**描述您想要的解决方案**文本字段，以与位置或其他类似的方式完成。

**描述你考虑过的替代方案**把它添加到我的个人资料中，但它会无处不在，把它作为结构化数据会很好。

**附加上下文**具有自由形式的信息字段也可能是更通用的选项；这对乳齿象很有效。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2365)

*   @rhymes 发布了一个问题，将 Google Lighthouse 添加到我们的 Travis CI 构建中。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加谷歌灯塔到特拉维斯 CI# 2370](https://github.com/thepracticaldev/dev.to/issues/2370)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-11T09:40:56Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2370)

**您的功能请求是否与某个问题相关？请描述一下。**

[谷歌灯塔](https://developers.google.com/web/tools/lighthouse/)是一个很好的工具，可以随时关注网页的性能和质量。但是，像所有工具一样，它需要定期检查。我提议通过 Travis CI 在 PRs 上激活[谷歌灯塔机器人](https://github.com/GoogleChromeLabs/lighthousebot)。

输出示例:

[![](img/3afcd2c4875b84f8c4bcfddb991e79e4.png)](https://user-images.githubusercontent.com/238208/46586553-dfffe600-ca34-11e8-9eea-3846b41a4360.png)

**描述您想要的解决方案**

机器人可以被激活，它可以在两种模式下运行:

*   显示人类消耗的摘要
*   显示摘要，如果预定义的指标低于给定的阈值，则 PR 失败。

我最初会启动第一步，将来一旦对这些指标有了更好的理解，可能会考虑失败的 PRs，因为所有工具都需要有所保留，并根据具体目标进行权衡。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2370)

*   @andrewbrown 发起了一场讨论，认为营销/静态页面应该与核心应用分离。谢谢， [@andrewbrown](https://dev.to/andrewbrown) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 营销页面应该与核心应用 #2377](https://github.com/thepracticaldev/dev.to/issues/2377) 

[![omenking avatar](img/c5b3096d42a99b0bcad72567d37cd4a0.png)](https://github.com/omenking) **[omenking](https://github.com/omenking)** posted on [<time datetime="2019-04-11T15:18:25Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2377)

**您的功能请求是否与某个问题相关？请描述一下。**

所以今天网络服务器关闭了，这是可能发生的事情，但它也关闭了静态页面。

**描述您想要的解决方案**

我建议将这些页面从 Rails 应用中分离出来，通过静态网站托管单独提供服务。

静态页面可以保留在回购协议中。

[![Screen Shot 2019-04-11 at 11 13 28 AM](img/b4074945f727cbc11ca90e9900971a07.png)](https://user-images.githubusercontent.com/7776/55968931-f2a8e100-5c4a-11e9-97db-2ebcff801155.png)

我建议静态网站托管与 S3。当然，我对 AWS 有偏见。但是不管提议的主机服务是什么，我认为这些页面不应该被服务器关闭。

**描述你考虑过的替代方案**既然 DO 是官方赞助商，那么他们的解决方案可能会更直白。

**附加上下文**添加一个状态为 https://github.com/ks888/LambStatus[的状态页面](https://github.com/ks888/LambStatus)非常简单

我很确定这在 AWS 免费层上不会花费什么，因为我在自己的 web 应用 ExamPro 上运行了它。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2377)

*   [@jankeromnes](https://dev.to/jankeromnes) 开启了关于考虑使用 Gitpod 预构建的讨论。谢谢， [@jankeromnes](https://dev.to/jankeromnes) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 考虑使用 Gitpod 预构建 #2380](https://github.com/thepracticaldev/dev.to/issues/2380) 

[![jankeromnes avatar](img/679a1be9e6f8a066b795a0663f37d93f.png)](https://github.com/jankeromnes) **[jankeromnes](https://github.com/jankeromnes)** posted on [<time datetime="2019-04-11T16:01:55Z">Apr 11, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2380)

**您的功能请求是否与某个问题相关？请描述一下。**

安装依赖项时等待是令人沮丧的。

**描述您想要的解决方案**

Gitpod 能够检测这个存储库中的变化，并通过检查新代码和提前安装依赖项来自动准备新的工作区。这样，当开发人员开始一个新的开发工作空间时，他们不必等待依赖项被安装——一切都已经检查完毕，可以开始了。

要启用预构建的工作区，请按照下列步骤操作:

1.  进入 [gitpod-io GitHub 应用](https://github.com/apps/gitpod-io)，点击`Configure`
2.  请选择该组织(`thepracticaldev`)和回购(`dev.to`)，点击`Install`
3.  您将被转到 Gitpod 以确认安装

**描述你考虑过的替代方案**

当前的设置已经通过自动安装依赖项节省了时间(只是没有提前)。

依赖项本身可以安装得更快，但这并不容易。

**附加上下文**

关于 Gitpod 预建工作区及其配置的更多信息:[https://www.gitpod.io/docs/46_Prebuilds/](https://www.gitpod.io/docs/46_Prebuilds/)

博文:等待代码构建就像看着油漆变干:[https://www.gitpod.io/blog/prebuilds/](https://www.gitpod.io/blog/prebuilds/)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2380)

*   [@desi](https://dev.to/desi) 要求徽章类型:评论条纹。感谢[@德西](https://dev.to/desi)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 功能请求:评论条纹徽章 #2419](https://github.com/thepracticaldev/dev.to/issues/2419) 

[![desirottman avatar](img/7a247594d3829a8ddf4907798c960169.png)](https://github.com/desirottman) **[desirottman](https://github.com/desirottman)** posted on [<time datetime="2019-04-12T19:36:50Z">Apr 12, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2419)

**您的功能请求是否与某个问题相关？请描述一下。**受[这篇关于写作条纹徽章和游戏化的博文](https://dev.to/mortoray/how-do-you-feel-about-chasing-internet-points-badges-and-the-gamification-of-everything-1l56)的启发，提供 4 周和 8 周评论条纹徽章可能也很有趣。这是鼓励互动的另一种方式，如果人们不愿意写自己的帖子，这也是一种低风险的方式。

**描述您希望**有趣的评论条纹徽章与写作条纹徽章共存的解决方案

**附加背景**只是一张我对 dev.to 社区的感受的 gif 图 [![masked wrestler applauding](img/791ea906455b87b34931eb1115ab5c8d.png)](https://camo.githubusercontent.com/075ad88e7deeb2837da2afbce31b321f124066f5/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f34367a76776a6c32757a6e4e5177656d72572f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2419)

*   @mariocsee 请求了一个在特定时间或日出/日落时自动切换主题的功能。谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 功能要求:在特定时间或日出日落  #2421](https://github.com/thepracticaldev/dev.to/issues/2421) 自动切换主题

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-12T20:13:57Z">Apr 12, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2421)

**您的功能请求是否与某个问题相关？请描述一下。**随着夜晚主题的引入，可能会有一些亮或暗的主题在特定时间自动切换，或者日出/日落作为一个选择加入的功能，类似于 Reddit 应用程序或夜班。夜晚主题仍在改进，所以也许当主题在 dev.to 上变得更加一致时，就是考虑构建这样一个功能的时候了。

**描述你想要的解决方案**也许用户能够确定他们的城市(日出/日落时间)或设置特定的时间在两个主题之间切换(目前有默认和夜间主题)。

**描述您考虑过的替代方案**iOS/Android 应用程序可能能够请求一些信息，因此在应用程序上更容易完成？

**附加上下文**不适用

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2421)

*   [@desi](https://dev.to/desi) 请求了一个有系列登陆页面的功能。谢谢，[@德西](https://dev.to/desi)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 功能请求:登陆  #2424](https://github.com/thepracticaldev/dev.to/issues/2424) 系列页面

[![desirottman avatar](img/7a247594d3829a8ddf4907798c960169.png)](https://github.com/desirottman) **[desirottman](https://github.com/desirottman)** posted on [<time datetime="2019-04-13T02:56:58Z">Apr 13, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2424)

**描述你想要的解决方案**如果系列文章可以有一个显示该系列所有文章的登陆页面，那就太酷了。它可以看起来像主提要，只有那些系列文章，而[个人文章本身](https://dev.to/aspittel/moving-past-tutorials-receiving-a-problem-to-solve-2hf8)可以点击系列名称——在这个例子中，“移动过去教程”系列文本的**部分将链接到系列文章的提要。**

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2424)

*   [@shindakun](https://dev.to/shindakun) 报告了一个帖子日期不正确的问题。谢谢， [@shindakun](https://dev.to/shindakun) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)# 2433](https://github.com/thepracticaldev/dev.to/issues/2433)发帖日期有误

[![shindakun avatar](img/0ec82f7d93fe0f152802630c5c9fc916.png)](https://github.com/shindakun) **[shindakun](https://github.com/shindakun)** posted on [<time datetime="2019-04-14T02:27:37Z">Apr 14, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2433)

**描述 bug**

我在比平时晚一点的时候发布了帖子，并注意到尽管现在是 2019 年 4 月 13 日星期六晚上 7 点 26 分(PDT)，但我的新帖子的日期是 4 月 14 日。时区是一种痛苦，我认为这毁了我的发帖记录。:((让我们切换到斯沃琪网络时间！).

**重现**

重现该行为的步骤:

1.  在太平洋标准时间晚上 7 点后创建帖子。
2.  注意，它被列为第二天。

[![Screen Shot 2019-04-13 at 7 18 06 PM](img/6dba1a564806fa0b91c7370af76ff41e.png)](https://user-images.githubusercontent.com/319128/56087477-26d5fa80-5e21-11e9-94ff-91549c29b72b.png)

**预期行为**

我还没有研究代码，但我认为用 UTC 保存新帖子并在浏览器中显示当地时间是一个解决方案，但这需要 moment.js 或类似的东西。

**截图**

[![clock punch](img/6651345e47937ae23ced9c6d3b23e5e3.png)](https://camo.githubusercontent.com/6f0754a1919012531f31c1a113f4fd0dc13f6d44/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f4c6f37584a34446a486a6b4f732f67697068792e676966)

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器:铬
*   版本:73.0.3683.75(官方版本)(64 位)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2433)

*   [@andrewbrown](https://dev.to/andrewbrown) 报告了视频回放导致错误的问题。谢谢， [@andrewbrown](https://dev.to/andrewbrown) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 视频播放器导致部分用户错误 #2436](https://github.com/thepracticaldev/dev.to/issues/2436) 

[![omenking avatar](img/c5b3096d42a99b0bcad72567d37cd4a0.png)](https://github.com/omenking) **[omenking](https://github.com/omenking)** posted on [<time datetime="2019-04-14T14:09:46Z">Apr 14, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2436)

**描述错误** 4 个不同的开发人员向用户报告，他们在使用视频播放器时收到一个错误。下面是对[帖子的一条评论，其中有一段视频是有问题的](https://dev.to/exampro/how-to-serve-on-demand-videos-from-aws-4fo4)

[![Screen Shot 2019-04-14 at 10 04 39 AM](img/fc4a053ffd744ca09fd302f3aed4a272.png)](https://user-images.githubusercontent.com/7776/56093998-efa52f00-5e9c-11e9-9dca-e2e7a291edea.png)

我将在未来几个月发布大量视频内容，所以我想我会不断听到关于视频功能的问题

我还不能复制这一期。不确定这是否与 OSX 和野生动物园有关。我刚好有新旧 MAC，无法复制这个错误。

[https://discussions.apple.com/thread/8501889](https://discussions.apple.com/thread/8501889)

```
Given I am a logged in user
and there is a post with video
When I try to play the video
Then I get an error 
```

**预期行为**不应发生任何错误

**截图**我无法提供截图，因为我无法重现该问题。

**桌面(请填写以下信息):**

*   操作系统:[例如 iOS]
*   浏览器[例如 chrome、safari]
*   版本[例如 22]

**智能手机(请填写以下信息):**

*   设备:[例如 iPhone6]
*   操作系统:[例如 iOS8.1]
*   浏览器[例如股票浏览器、safari]
*   版本[例如 22]

**附加上下文**在此处添加关于问题的任何其他上下文或有用的链接。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2436)

*   [@bnb](https://dev.to/bnb) 报告了阅读列表的评论视图没有工作反应按钮的问题。谢谢， [@bnb](https://dev.to/bnb) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 阅读列表评论查看:点击心脏不触发一个喜欢 #2445](https://github.com/thepracticaldev/dev.to/issues/2445) 

[![bnb avatar](img/cfbb40f014d9004f5cd1da04ec9fe210.png)](https://github.com/bnb) **[bnb](https://github.com/bnb)** posted on [<time datetime="2019-04-15T12:29:02Z">Apr 15, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2445)

**描述 bug** 看起来试图 <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> 对[阅读列表评论视图](https://dev.to/readinglist?v=comments)进行评论目前实际上并没有 <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> 该评论——相反，它只是选择了它。

**重现**重现行为的步骤:

1.  转到'[https://dev.to/readinglist?v=comments](https://dev.to/readinglist?v=comments)
2.  点击任何评论上的 <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> 图标。
3.  您会看到它被突出显示。

**预期行为**应该添加一个新的 like。

**截图** [![image](img/d138cc6cb0d4f88499a96d5b531cfbe0.png)](https://user-images.githubusercontent.com/502396/56132575-55a2bc80-5f58-11e9-8785-883b720226d5.png)

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器:铬
*   版本 73.0.3683.103

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2445)

*   [@venarius](https://dev.to/venarius) 就`*.jsx`文件上的 ESLint 错误发表了一个问题。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)*上的 Eslint 错误。jsx 文件 #2470](https://github.com/thepracticaldev/dev.to/issues/2470) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-04-17T07:16:08Z">Apr 17, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2470)

**描述 bug** 这是 https://github.com/thepracticaldev/dev.to/pull/2432[的后续](https://github.com/thepracticaldev/dev.to/pull/2432)

*。`app/javascript`里面的 jsx 文件按照 eslint 的说法还是有很多不好的做法。这就是为什么当你编辑这些文件时，eslint 会在提交前的 husky hook 期间抛出大约 650 个错误。

**重现**重现行为的步骤:

进入`app/javascript`并运行`eslint . --ext .jsx`

**预期行为**不应该有任何 eslint 错误。

**截图** [![Screenshot 2019-04-17 at 09 12 36](img/479d85df21661c019ac3ad1a42f1844d.png)](https://user-images.githubusercontent.com/13546486/56268129-fc21c580-60f0-11e9-9e18-b18a1ed15c67.png)

*在我重构了[https://github.com/thepracticaldev/dev.to/pull/2432](https://github.com/thepracticaldev/dev.to/pull/2432)T3】中报告的问题代码气候后，我将开始处理这些错误*

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2470)

*   [@ben](https://dev.to/ben) 发表了一篇关于消除与随机化我们的测试顺序相关的可能失败的文章。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 排除与随机规格订单相关的可能故障 #2483](https://github.com/thepracticaldev/dev.to/issues/2483) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-18T14:05:30Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2483)

我们最近启用了随机规格排序，这使我们的规格具有更好的长期稳定性:

[https://github.com/thepracticaldev/dev.to/pull/2466](https://github.com/thepracticaldev/dev.to/pull/2466)

我相信他们确实是在揭示基于排序的随机故障。我认为我们应该:

*   设法确认故障确实是由于随机排序造成的
*   当我们解决问题时，可能会暂时切换回非随机排序
*   解决与随机订购相关的问题

想法？

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2483)

*   [@mariocsee](https://dev.to/mariocsee) 报告了一个 bug，在 Connect group 频道中报告用户滥用会显示不正确的信息。谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 举报用户在接群聊中的错误信息 #2489](https://github.com/thepracticaldev/dev.to/issues/2489) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-18T18:00:12Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2489)

**描述 bug** 在`/connect/`的群聊中查看个人资料时，举报滥用按钮没有更新信息，举报时会传递群聊名称而不是特定用户。

**重现**重现行为的步骤:

1.  转到`/connect/General`(或 DEV 上的任何群组聊天)
2.  单击组名以打开该组的详细信息
3.  单击任何用户以打开用户详细信息
4.  点击举报滥用
5.  看到错误的信息，因为你不一定有一个单独的直接与群聊中的所有用户沟通的渠道
6.  点击是，报告虐待
7.  查看链接字段填充的是`/connect/General`或任何组名，而不是您正在查看的特定用户

**预期行为**消息应调整到适当的上下文，报告滥用字段应包含特定用户。

**截图** [![Screen Shot 2019-04-18 at 13 53 40](img/7efc878fe74da14bb2c3d3eea872644b.png) ](https://user-images.githubusercontent.com/13403332/56381209-38811e80-61e2-11e9-871d-3760a86f938e.png) [ ![Screen Shot 2019-04-18 at 13 53 57](img/bf78f1c934320efa45ee0b1a08cde611.png)](https://user-images.githubusercontent.com/13403332/56381212-3ae37880-61e2-11e9-88c2-c0b58140e8d6.png)

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器浏览器
*   版本 73

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2489)

*   [@rugk](https://dev.to/rugk) 请求了一个功能，v1 编辑器的标题在顶部变得有粘性，以便于导航。谢谢，[@ rugg](https://dev.to/rugk)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 编辑按钮应该是固定的 #2490](https://github.com/thepracticaldev/dev.to/issues/2490) 

[![rugk avatar](img/15c238948ca3753e0d27662f89aface7.png)](https://github.com/rugk) **[rugk](https://github.com/rugk)** posted on [<time datetime="2019-04-18T18:14:53Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2490)

**您的功能请求是否与某个问题相关？请描述一下。**我很沮丧，如果我重新阅读我的文本，并很快想改变什么，我必须滚动 uüp 并点击“编辑”。类似地，我可能也想在预览中阅读文本，在那里我也必须一直向上滚动…

**描述您想要的解决方案**将按钮固定，这样我就可以一直点击它们，并且*停留在当前位置*(页面滚动)！

**描述你考虑过的替代方案**不适用

**附加语境**似乎显而易见。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2490)

*   [@andrewbrown](https://dev.to/andrewbrown) 请求了一个功能，可以为带有视频的帖子设置缩略图/视频图像。谢谢， [@andrewbrown](https://dev.to/andrewbrown) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 可以为视频帖子设置缩略图/视频图像 #2494](https://github.com/thepracticaldev/dev.to/issues/2494) 

[![omenking avatar](img/c5b3096d42a99b0bcad72567d37cd4a0.png)](https://github.com/omenking) **[omenking](https://github.com/omenking)** posted on [<time datetime="2019-04-19T04:02:41Z">Apr 19, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2494)

**您的功能请求是否与某个问题相关？请描述一下。**

当你有一个视频帖子时，它总是获取视频的第一帧。所以我必须编辑第一帧，以获得我想要出现的内容。我总是忘记这一点，并重新处理我的视频只是为了开发和重新上传。

[![Screen Shot 2019-04-18 at 11 58 27 PM](img/26d62de2d89f48c21eb9ade4df537752.png)](https://user-images.githubusercontent.com/7776/56404694-1e712b80-6236-11e9-88be-55b4e14c8643.png)

**描述您希望**能够上传缩略图/默认图像的解决方案

**描述你考虑过的替代方案**没有

**附加上下文**无

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2494)

*   [@lightalloy](https://dev.to/lightalloy) 给`ActiveJob`开了一个综述移动异步方法的问题。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将异步方法移动到活动作业 #2497](https://github.com/thepracticaldev/dev.to/issues/2497) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-19T13:39:18Z">Apr 19, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2497)

目前，大多数异步方法直接使用`DelayedJob`。出于几个原因，使用 ActiveJob 而不是`DelayedJob`会更好:

*   迁移到另一个队列后端会更容易，比如 sidekiq
*   指定队列和设置特定类型作业的优先级更容易
*   通常，将逻辑转移到一个单独的任务需要重构，结果代码具有更好的设计
*   更容易避免反序列化错误(#1621)

DelayedJob 运行的方法有 [2 种:](https://github.com/collectiveidea/delayed_job#queuing-jobs)

*   `handle_asynchronously`
*   `delay`

已经有一张通知活动作业的传票:#1996

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2497)

# 开发-iOS

我们最近没有任何新的问题或 PRs 合并。请随意查看 [iOS 回购](https://github.com/thepracticaldev/dev-ios)，或者在 App Store 下载[我们的 iOS 应用。](https://itunes.apple.com/us/app/dev-community/id1439094790)

本期就到这里！敬请关注周五的另一篇文章！