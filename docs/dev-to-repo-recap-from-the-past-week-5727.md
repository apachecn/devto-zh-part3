# 开发人员对上周的回购进行总结

> 原文：<https://dev.to/devteam/dev-to-repo-recap-from-the-past-week-5727>

欢迎回到另一个回购回顾，我们涵盖了上周对 [dev.to 的仓库](https://github.com/thepracticaldev/dev.to)、[iOS 回购](https://github.com/thepracticaldev/dev-ios)的贡献。这一期涵盖 4 月 20 日至 4 月 26 日。

但首先，一些新闻:我们有一个官方的 Android 应用程序，并与它一起，一个新的回购添加到这个系列！查看变更日志，了解所有详细信息:

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/1110d28955bc625854f9cb7fe9b08fd6.png)](/devteam) [## 宣布 Android 开发

### 开发团队的 MAC Siri 2019 年 4 月 24 日 1 分钟阅读

#android #meta](/devteam/announcing-dev-for-android-4c3j)

[![Get it on Google Play](img/36a0d9330742816ba257ac3838c105e6.png)](https://play.google.com/store/apps/details?id=to.dev.dev_android&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1)

回到主回购:

# 特性

*   您现在可以在您的个人资料链接中添加 Twitch URL 了！感谢 [@bolariinwa](https://dev.to/bolariinwa) 的补充。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 给个人资料添加抽动链接 #2557](https://github.com/thepracticaldev/dev.to/pull/2557) 

[![bolariin avatar](img/a5411278d9eefe6f01d3157b0ad4f809.png)](https://github.com/bolariin) **[bolariin](https://github.com/bolariin)** posted on [<time datetime="2019-04-25T19:38:38Z">Apr 25, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2557)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将 Twitch 添加到个人资料链接列表中。为了实现，我按照其他配置文件链接的模式来包含 twitch。

## 相关车票&单据

解决#2505

## 手机&桌面截图/录音(如有 UI 改动)

[![Twitch now in Profile Links](img/59dfff27ceaf6690fa296685e6aa3c0d.png)](https://user-images.githubusercontent.com/24629960/56763041-5402a100-676f-11e9-8085-a5416ec37263.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2557)

*   新评论和新帖子通知现在有了时间戳！感谢 [@kdraypole](https://dev.to/kdraypole) 的功能。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 给评论和帖子通知添加时间戳 #2565](https://github.com/thepracticaldev/dev.to/pull/2565) 

[![KDRaypole avatar](img/6e5e1885a6b641aa2bd97c410b366844.png)](https://github.com/KDRaypole) **[KDRaypole](https://github.com/KDRaypole)** posted on [<time datetime="2019-04-26T02:47:17Z">Apr 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2565)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

该提交将时间戳添加到评论和发布通知中。我决定使用`time_ago_in_words`方法，而不是使用`strftime`和排序日期。我认为这看起来更好，更准确，当谈到最近收到的通知。不过，最终决定权在主要贡献者，如果你想让我把它改回来，请告诉我。

## 相关车票&单据

[https://github.com/thepracticaldev/dev.to/issues/2010](https://github.com/thepracticaldev/dev.to/issues/2010)

[![Screen Shot 2019-04-25 at 10 26 19 PM](img/028e3a36716549ed811c686c21e568ea.png)](https://user-images.githubusercontent.com/25459752/56780096-f6407a00-67ab-11e9-9bee-8a92ae303f72.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2565)

# Bug 修复/其他贡献

*   [@nickytonline](https://dev.to/nickytonline) 禁用`console.log`林挺法则，开新一期讨论。谢谢， [@nickytonline](https://dev.to/nickytonline) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 禁用当前记录错误的无控制台。 #2502](https://github.com/thepracticaldev/dev.to/pull/2502) 

[![nickytonline avatar](img/849ad90a82f294a93718c2c4da688f82.png)](https://github.com/nickytonline) **[nickytonline](https://github.com/nickytonline)** posted on [<time datetime="2019-04-19T23:00:07Z">Apr 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2502)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

禁用了使用`console.log`报告错误的行的`no-console`规则。

## 相关车票&单据

#2501, #1828

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![Aubrey Plaza "There are no rules"](img/4443fd143e44e4426e3c8fe1f7383dcb.png)](https://camo.githubusercontent.com/c26612ac3bee46cc1cb64003c3a61896226e3b27/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6c304d597458506b6c41557150554746572f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2502)

*   [@nickytonline](https://dev.to/nickytonline) 也在我们的文档中增加了`rbenv`故障排除部分。(`rbenv`我们建议的用于 macOS / Linux 的 Ruby 环境管理工具)。再次感谢你，尼克！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 增加了 rbenv 故障排除章节。 #2479](https://github.com/thepracticaldev/dev.to/pull/2479) 

[![nickytonline avatar](img/849ad90a82f294a93718c2c4da688f82.png)](https://github.com/nickytonline) **[nickytonline](https://github.com/nickytonline)** posted on [<time datetime="2019-04-18T01:54:32Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2479)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [X]文档更新

## 描述

添加了 rbenv 无法安装 ruby 2 . 6 . 1 版的故障排除部分。自从圣诞节以来，我就没有在本地运行过应用程序(2.5.1 版)，所以我运行了`rbenv install 2.6.1`,却得到了以下错误:

```
ruby-build: definition not found: 2.6.1
See all available versions with `rbenv install --list'.                       If the version you need is missing, try upgrading ruby-build:
```

Enter fullscreen mode Exit fullscreen mode

文档的更新解释了如何解决这个问题。如果你认为合适，可以随意修改措辞。

## 添加到文档中？

*   [x]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![](img/261c393c2b952d3d89aafa538be72b85.png)](https://camo.githubusercontent.com/1e62fbe9b52b5bb3837fd1aeca03d7fff0398cb8/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f737943613569726437777030632f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2479)

*   @lightalloy 修复了一个错误，反应通知可以用无效数据创建。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 防止创建空 json_data   #2516](https://github.com/thepracticaldev/dev.to/pull/2516) 的反应通知

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-22T12:27:19Z">Apr 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2516)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [x]错误修复

## 描述

针对#2124 的快速修复，由于#2213 需要很长时间来合并此 pr，因此应该修复使用空`json_data`创建通知的问题，更改如下:

*   使 1 在创建反应通知时创建查询，而不是 2(创建+更新)

## 相关车票&单据

#2124

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2516)

*   [@押韵](https://dev.to/rhymes)移动一颗未使用的红宝石`fake_stripe`。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 清除未使用的宝石假 _ 条纹 #2517](https://github.com/thepracticaldev/dev.to/pull/2517) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-22T15:46:57Z">Apr 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2517)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

我找不到宝石[假 _ 条纹](https://rubygems.org/gems/fake_stripe)被使用的地方，它很可能已经被[条纹-红宝石-假](https://rubygems.org/gems/stripe-ruby-mock)完全取代，但被留下了。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2517)

*   [@cyrillefr](https://dev.to/cyrillefr) 将`send_welcome_notification`方法移动到了`ActiveJob`中，作为将`DelayedJob`方法移动到`ActiveJob`的持续努力的一部分。谢谢， [@cyrillefr](https://dev.to/cyrillefr) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移动欢迎通知到活动岗位 #2485](https://github.com/thepracticaldev/dev.to/pull/2485) 

[![cyrillefr avatar](img/7ef3538029145d4cf304bfb0f0cca6f5.png)](https://github.com/cyrillefr) **[cyrillefr](https://github.com/cyrillefr)** posted on [<time datetime="2019-04-18T15:31:21Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2485)

*   欢迎通知的创建由自己负责
*   由活动作业调用

决议:#1996

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将欢迎通知移至活动工单

## 相关车票&单据

第 1996 期

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2485)

*   为我们的邮寄者增加了测试。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加跟踪邮件人规格 #2523](https://github.com/thepracticaldev/dev.to/pull/2523) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-23T12:22:57Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2523)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

我正计划将`ahoy_email`升级到它的最新版本，其中包含了突破性的变化，所以我认为在尝试这样做之前，最好确保有邮件跟踪的测试。

在这个过程中，我删除了`new_report_email`和`reporter_resolution_email`，因为它们没有关联的模板，也没有在任何地方使用。

我还在直接消息邮件中添加了`.html_safe`来显示实际的 HTML，而不是标签。

对`seeds.rb`的添加是为了让预览邮件程序不会在本地失败，因为不存在对它们进行渲染的要求。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2523)

*   [@韵](https://dev.to/rhymes)升级了一堆宝石。再次感谢，[@韵](https://dev.to/rhymes)！列表如下:

    *   `capybara`到 3.18
    *   `factory_bot_rails`至 5.0.2
    *   `emoji_regex`到 2.0
    *   `ahoy_email`至 1.03
    *   `airbrake`至 9.1.0
    *   `brakeman`至 4.5
    *   `dry-struct`到 1.0
*   [@kdraypole](https://dev.to/kdraypole) 修复了标签页上的侧边栏小工具链接在夜间模式下有不可读文本的问题。谢谢， [@kdraypole](https://dev.to/kdraypole) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将主题颜色应用到侧边栏小工具链接 #2538](https://github.com/thepracticaldev/dev.to/pull/2538) 

[![KDRaypole avatar](img/6e5e1885a6b641aa2bd97c410b366844.png)](https://github.com/KDRaypole) **[KDRaypole](https://github.com/KDRaypole)** posted on [<time datetime="2019-04-23T23:49:51Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2538)

# 这是什么类型的公关？

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

该提交更新侧栏小部件链接，以应用适当的主题颜色。这将防止链接由于夜间主题的低对比度而难以阅读。

[![Screen Shot 2019-04-23 at 7 57 26 PM](img/aa684e424672321461028271fe388a73.png)](https://user-images.githubusercontent.com/25459752/56623392-09afe180-6602-11e9-988e-feefdc5db67b.png)

[![Screen Shot 2019-04-23 at 7 32 09 PM](img/e2a4b24e2722d84e4d8e9e26b272bbdb.png)](https://user-images.githubusercontent.com/25459752/56623382-fef54c80-6601-11e9-86d5-e3c35c9db2de.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2538)

*   [@mjraadi](https://dev.to/mjraadi) 修复了仪表板中“上传视频”和排序下拉菜单的位置 UI 错误。谢谢， [@mjraadi](https://dev.to/mjraadi) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复仪表盘 UI 定位 Bug #2506  #2522](https://github.com/thepracticaldev/dev.to/pull/2522) 

[![mjraadi avatar](img/4244e10cdc945dace6882cda739d9b26.png)](https://github.com/mjraadi) **[mjraadi](https://github.com/mjraadi)** posted on [<time datetime="2019-04-23T12:06:30Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2522)

*   移除清除修复`div`
*   移除浮动属性
*   将 Flexbox 属性添加到父容器
*   为小于 426 像素的屏幕添加媒体查询，并相应调整样式
*   拉伸并居中`upload a video` & `sort`选择。在 Chrome 和 Firefox 上测试。
*   根据其他元素调整`sort`选择框的字体系列。

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

我使用 Flexbox 来解决定位问题，因为它已经用于`dashboard` UI 中的其他元素。这两个元素被拉伸并集中在小屏幕设备上，对我来说，这比集中并占据屏幕一半宽度要好得多。`select`元素已经居中，在 Chrome 和 Firefox 上运行良好，但在 Safari 或 Edge 上可能不工作。

## 手机&桌面截图/录音(如有 UI 改动)

### 这个补丁在大屏幕上看起来怎么样

[![Screenshot_20190423_162138](img/5494fda250b829eb2e1d8bec546aaa9f.png)](https://user-images.githubusercontent.com/20579660/56578936-0efe3380-65e4-11e9-900a-c6197206faf2.png)

### 426 px 断点后的样子

[![Screenshot_20190423_162127](img/65708adb48704872f3bd010c2f53d926.png)](https://user-images.githubusercontent.com/20579660/56579026-51277500-65e4-11e9-8084-4f24bd976332.png)

### 426 px 断点前是什么样子

[![Screenshot_20190423_163517](img/281695dafe8a3e3a965ee03915630fd0.png)](https://user-images.githubusercontent.com/20579660/56579612-e5460c00-65e5-11e9-8f3c-65eb4541e954.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2522)

*   [@本](https://dev.to/ben)修复了 [`/videos`](https://dev.to/videos) 上视频图像无法正常加载的小问题。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修正画卷上的风格问题视频图像 #2551](https://github.com/thepracticaldev/dev.to/pull/2551) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-24T22:09:38Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2551)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

小 UI 修复

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2551)

*   [@lightalloy](https://dev.to/lightalloy) 给我们的`BlackBox`增加了测试。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 黑盒规格# 2524# 2542T6】](https://github.com/thepracticaldev/dev.to/pull/2542)

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-24T13:42:35Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2542)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [x]错误修复

## 描述

*   增加了`BlackBox`和`FunctionCaller`的规格
*   创建了一个`Aws::FakeClient`在非生产环境中不实际调用 aws lambda 我可以在测试环境中使用 webmock 或 VCR 来模拟调用，但是实际调用也不应该在开发环境中进行。
*   修正了从 aws lambda 函数返回`nil`时的错误
*   这个 pr 还修复了在开发环境中作为维护成员登录的`/settings/membership`页面的打开

## 相关车票&单据

#2524

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2542)

*   [@glennmen](https://dev.to/glennmen) 将组织添加到以下总计数中。谢谢， [@glennmen](https://dev.to/glennmen) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加组织到总以下计数 #2544](https://github.com/thepracticaldev/dev.to/pull/2544) 

[![Glennmen avatar](img/b208733bf7be977cfd1b09cfcfd1f625.png)](https://github.com/Glennmen) **[Glennmen](https://github.com/Glennmen)** posted on [<time datetime="2019-04-24T18:07:43Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2544)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

在#2157 中，被关注的组织作为一个单独的类别添加到下一页，但该计数未添加到总关注计数中。

## 手机&桌面截图/录音(如有 UI 改动)

之前: [![Screenshot at Apr 24 19-56-57](img/717c0c0ad9a623eb8ae17f8dfd15c9a3.png)](https://user-images.githubusercontent.com/11406433/56682617-3af4e400-66cc-11e9-921d-ee14047afd74.png)

[![Screenshot at Apr 24 20-00-35](img/374af8acb79f650bd5005871134e173d.png)](https://user-images.githubusercontent.com/11406433/56682636-447e4c00-66cc-11e9-8a2b-dc852491bd37.png) 之后

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2544)

*   [@mariocsee](https://dev.to/mariocsee) 修复了你无法对阅读列表评论活动部分的评论做出正确反应的问题。谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修罗阅读榜评论反应 #2493](https://github.com/thepracticaldev/dev.to/pull/2493) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-18T21:11:49Z">Apr 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2493)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

此处描述的问题:#2445

目前，`/readinglist?v=comments`中的注释是通过`initializeCommentsPage.js.erb`加载的。方法`initializeCommentsPage()`寻找 ID 为`comments-container`且属性为`data-commentable-id`和`data-commentable-type`的元素，但是该元素在阅读列表页面中不存在。

当查看其他带有评论的页面时，它们有这样一个元素:

```
<div class="comments-container" id="comments-container" data-commentable-id="23" data-commentable-type="Article">
...
</div> 
```

这允许`initializeCommentsPage()`将适当的 onClick 监听器附加到每个评论反应上。

目前正在尝试如何解决这个问题。会更新！

更新(4 月 22 日):

*   调整了阅读列表注释 HTML 的构建方式，添加了`data-commentable-id=reading_list_ids`和与“注释容器”相同的类和 id，即- >`
*   调整了`initializeCommentsPage()`方法来处理对多个 id 的解析，就像上面的 div 一样，并且必须限定异步请求的范围，以从每个 id 获得正确的反应
*   通过添加`class="comments-container"`,阅读列表的评论现在有了风格
*   注释有合适的 like count，reacted states 和 onclicklisteners，并按照它们应该的方式工作

## 相关车票&单据

解决#2445

## 手机&桌面截图/录音(如有 UI 改动)

[![Screen Shot 2019-04-22 at 16 57 53](img/f97afcaf8c7ad72f74e954b1394f8e2b.png)](https://user-images.githubusercontent.com/13403332/56530504-781c7300-6520-11e9-90ab-be143b8f9430.png)

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2493)

*   [@jess](https://dev.to/jess) 解决了一个问题，即只有当用户有电子邮件地址时才取消订阅。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 放逐用户:只有当用户有邮箱地址  #2556](https://github.com/thepracticaldev/dev.to/pull/2556) 时才取消订阅

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-04-25T19:26:23Z">Apr 25, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2556)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

如果一个垃圾邮件帐户没有电子邮件地址，不要尝试从 mailchimp 取消订阅。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2556)

*   [@rhymes](https://dev.to/rhymes) 修复了`AWS_LAMBDA`常量变量被初始化两次的问题。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复双 AWS_LAMBDA 初始化 #2570](https://github.com/thepracticaldev/dev.to/pull/2570) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-26T08:53:41Z">Apr 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2570)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

Rails 服务器报错，警告常量`AWS_LAMBDA`有双重初始化:

```
./config/initializers/aws_sdk.rb:1: warning: already initialized constant AWS_LAMBDA
./config/initializers/aws_sdk.rb:8: warning: previous definition of AWS_LAMBDA was here
```

Enter fullscreen mode Exit fullscreen mode

这就解决了问题

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2570)

*   [@lightalloy](https://dev.to/lightalloy) 送了些死代码去墓地。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除了未使用的代码 #2567](https://github.com/thepracticaldev/dev.to/pull/2567) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-26T08:00:47Z">Apr 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2567)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

#2542 后的清理

*   取出不用的录像机磁带
*   移除空的构造函数

## 相关车票&单据

#2542

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2567)

*   [@韵](https://dev.to/rhymes)移除了一个未使用的`timeString`变量。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除了未使用的 timeString 语句 #2568](https://github.com/thepracticaldev/dev.to/pull/2568) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-26T08:03:38Z">Apr 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2568)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

在一次回顾中，我注意到了这样的陈述，我四处查看了一下，它们似乎在一些地方被复制和粘贴，但从未被使用过。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2568)

*   [@rhymes](https://dev.to/rhymes) 移除了`FlipFlop`宝石的最后残余，我们用它来开启和关闭某些功能。再次感谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 清除最后一丝残余的触发器【2585 号](https://github.com/thepracticaldev/dev.to/pull/2585)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-26T22:52:05Z">Apr 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2585)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

正如#2573 关于@ jessleenyc 的讨论，我发一个小的 PR 来删除一些(已经被忽略的)使用 Flipflop 的测试，这些测试已经被@Zhao-Andy 在的[中删除了](https://github.com/thepracticaldev/dev.to/pull/1358)

## 相关车票&单据

参考编号 2573

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2585)

*   [@jess](https://dev.to/jess) 修正了合并两个账号时侧边栏的缓存无法正常清除的问题。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 清除用户合并工具条上的缓存 #2562](https://github.com/thepracticaldev/dev.to/pull/2562) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-04-25T22:33:29Z">Apr 25, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2562)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

合并用户时，侧边栏不会更新。这应该会更新这些区域中的 rails 缓存。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2562)

# 新的问题和讨论

*   @rhymes 打开了一个讨论来添加关于我们的 JavaScript 前端的开发者文档。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加关于 JavaScript 前端的开发者文档 #2507](https://github.com/thepracticaldev/dev.to/issues/2507) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-21T19:10:09Z">Apr 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2507)

**您的功能请求是否与某个问题相关？请描述一下。**

紧随[关于迁移到 TypeScript 和提高前端开发代码库整体质量的想法](https://dev.to/ben/thoughts-on-migrating-to-typescript-and-improving-the-overall-quality-of-the-frontend-dev-codebase-1121)，[开发人员注意:不要忘记清除缓存！https://github.com/thepracticaldev/dev.to/issues/2499](https://dev.to/jess/dev-notes-don-t-forget-to-clear-cache-962)和我认为如果有一些关于前端部分(尤其是 JavaScript 层)如何组合在一起的概述会很好，也很有用。

我知道有两套 JS 代码库，一套是由 Rails 的链轮管理的[app/assets/JavaScript](https://github.com/thepracticaldev/dev.to/tree/master/app/assets/javascripts)中的“遗产”，另一套是由 webpack 管理的 [app/javascript](https://github.com/thepracticaldev/dev.to/tree/master/app/javascript) 中的“遗产”。

除此之外，我不确定一切是如何工作的(我承认我没有在前端花太多时间，除了偶尔的错误修复或小功能)。

`docs/frontend/javascript.md`可能会回答的几个问题:

*   初始化是如何工作的？
*   Preact 层是不是完全不了解香草 JS 层？
*   两个 JS 代码库是否相互交互/调用？
*   它们是如何附加到模板页面上的？每个网页是否提供两套压缩/缩小的 JS 文件？
*   服务人员在开发到环境中做什么？是登记在整页上吗？是不是不止一个？
*   边缘缓存如何适应这一切？
*   即时点击在这一切中有什么作用？

加上其他重要的东西。它不需要有很深的深度，只需要一个知道什么做什么以及所有东西如何组合在一起的藏宝图。

**描述您想要的解决方案**

包含 JS 前端如何工作的描述的文档文件

**描述你考虑过的替代方案**

我还没有考虑替代方案 TBH，现状还可以，如果有一些关于代码基础的入门文档，对贡献者来说会更容易，特别是如果目标是重构它，使它现代化，甚至用 TypeScript 或其他解决方案进行调整。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2507)

*   [@12vanblart](https://dev.to/12vanblart) 报告了一个文章编辑器中导航条与文本输入重叠的 bug。谢谢，@12vanblart！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)T2【nav bar】在新增帖子页面上突出输入字段#2519](https://github.com/thepracticaldev/dev.to/issues/2519) 

[![12vanblart avatar](img/1fe4ab4bcfb01224e3c584027788f8a6.png)](https://github.com/12vanblart) **[12vanblart](https://github.com/12vanblart)** posted on [<time datetime="2019-04-23T00:39:38Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2519)

**描述 bug** 当在[https://dev.to/new](https://dev.to/new)上时，如果占据了足够的垂直空间，可以使用箭头键来导航导航条后面的光标。

**重现**重现行为的步骤:

1.  前往[https://dev.to/new](https://dev.to/new)
2.  添加几段文字(足以让你滚动屏幕上的“标题”输入——我可以推荐[https://www.lipsum.com/feed/html](https://www.lipsum.com/feed/html))
3.  尽可能向下滚动
4.  在输入中单击并使用箭头键将光标向上移动到导航栏后面。

**预期行为**导航条不应该与输入框重叠(如果页面刷新后没有保存，也不会重叠)。

**桌面(请填写以下信息):**

*   OS: Windows 和 Chrome 操作系统
*   浏览器:铬
*   版本:73.0.3683.88(正式版)(64 位)[Chromebook] -在 windows 上不确定，但应该是最新版本。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2519)

*   我报告了一个错误，组织侧边栏的 CTA 可能会被错误地缓存。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 组织侧栏 CTA 可能缓存不正确 #2520](https://github.com/thepracticaldev/dev.to/issues/2520) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-04-23T01:07:31Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2520)

作为一个组织，我可以在我的边栏中添加一个 CTA。然而，如果我以前发表过一个帖子，然后我继续添加一个新的 CTA 或更新以前的一个，它可能不会显示在我以前的帖子中。

我们可能想要这样的东西:

```
class OrganizationsController
   # ... etc code
  def update
    organization = Organization.find(params[:id])
    ActiveRecord::Base.transaction do
      organization.update(org_params) # etc etc
      cb = CacheBuster.new
      organization.articles.pluck(:path).each { |path| cb.bust(path) }
    end
  end
end
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以将`Rails.cache`时间戳添加到组织侧边栏。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2520)

*   [@bolariinwa](https://dev.to/bolariinwa) 报告了一个 UI 问题，在 v2 文章编辑器中，“保存更改”文本从按钮本身溢出。谢谢@bolariinwa！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【保存修改】v2 中的文字溢出按钮 #2521](https://github.com/thepracticaldev/dev.to/issues/2521) 

[![bolariin avatar](img/a5411278d9eefe6f01d3157b0ad4f809.png)](https://github.com/bolariin) **[bolariin](https://github.com/bolariin)** posted on [<time datetime="2019-04-23T04:41:38Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2521)

**描述 bug** 在 v2 编辑器中编辑帖子时，保存更改按钮内的文本溢出按钮。

**重现**重现行为的步骤:

1.  切换到 v2 编辑器
2.  单击现有帖子的编辑或发布帖子，然后单击编辑
3.  您会注意到下面的“保存更改”按钮完全去掉了“S”

**预期行为**预期按钮内包含保存更改文本。

**截图** [![Save Changes text spills](img/d0deb16f444ee4fb89fbc6856351bb2a.png)](https://user-images.githubusercontent.com/24629960/56554164-c9e3ee00-655e-11e9-9360-d8888d3b7825.png)

**桌面(请填写以下信息):**

*   操作系统:Ubuntu
*   浏览器:铬
*   版本 73.0.3683.103

**附加上下文**在此处添加关于问题的任何其他上下文或有用的链接。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2521)

*   [@lightalloy](https://dev.to/lightalloy) 报告了一个 bug，其中一些`Notifications::NewReactionJobs`失败，并显示错误消息:`save!`上“验证失败”。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 某些通知::NewReactionJobs 失败，保存时显示“验证失败”！ #2525](https://github.com/thepracticaldev/dev.to/issues/2525) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-23T13:39:07Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2525)

一些`Notifications::NewReactionJobs`失败，在`Notifications::Reactions::Send#call`中`Validation failed: User has already been taken`开启`save!`

也许当一个用户在一篇文章上创建几个反应，几个作业被创建，并且这些作业同时运行时，就会发生这种情况。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2525)

*   @mariocsee 报告了一个 bug，夜间主题中的赞助商图片没有透明背景。谢谢，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 赞助商形象之夜主题 #2535](https://github.com/thepracticaldev/dev.to/issues/2535) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-23T17:47:10Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2535)

**描述 bug** 一些赞助商图片有白色背景，与夜晚主题和未来可能的主题冲突。使用夜间主题在较暗的环境中浏览的观众可能会受到白色背景的困扰。

虽然您可以在`settings/misc`中隐藏赞助商，但我认为赞助商消息传递与样式匹配得很好，可以在支持帮助开发的组织的同时提供流畅的体验。

**重现**重现行为的步骤:

1.  将您的设置/杂项设置为夜间主题
2.  转到主页
3.  看赞助商部分

**预期行为**如果赞助商有额外的图片(清晰的背景或浅色文本/深色背景),要求与夜晚主题页面(以及其他较暗的主题)相匹配。根据主题交换赞助商图片。

**截图**如果适用，添加截图来帮助解释您的问题。

**桌面(请填写以下信息):**

### 赞助商形象在白色背景下脱颖而出。

[![Screen Shot 2019-04-23 at 13 32 34](img/f9e882c7fb8dec2ead81557b30c69d67.png)](https://user-images.githubusercontent.com/13403332/56603628-14e91a00-65ce-11e9-9a9f-309b336ea8d2.png)

*   操作系统:苹果操作系统
*   浏览器浏览器
*   版本 73

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2535)

*   [@gajus](https://dev.to/gajus) 报告了一个 bug，他们在通过 API 访问帖子时收到了一个`500`。谢谢， [@gajus](https://dev.to/gajus) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  500 内部服务器通过 API   #2536](https://github.com/thepracticaldev/dev.to/issues/2536) 访问文章时出错

[![gajus avatar](img/79cfc97cf10d46912179e78e79ea245a.png)](https://github.com/gajus) **[gajus](https://github.com/gajus)** posted on [<time datetime="2019-04-23T17:56:51Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2536)

例如

```
$ curl https://dev.to/api/articles/101813
{"status":500,"error":"Internal Server Error"}

```

Enter fullscreen mode Exit fullscreen mode

尚不清楚这是否表明该内容将在以后可以访问，或者是否存在真正的服务器错误。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2536)

*   我报告了一个 bug，Safari 中一个帖子的动作工具条与其他浏览器不一致。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)Safari 中的文章工具条与其他浏览器不一致 #2537](https://github.com/thepracticaldev/dev.to/issues/2537) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-04-23T18:30:14Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2537)

在 Safari 桌面(13 英寸或更大，不知道像素限制是多少)中，文章边栏看起来有点不合适:

### 游猎

[![Sidebar in Safari](img/492e36444bcfae46baee6f824db93bf5.png)](https://user-images.githubusercontent.com/17884966/56606401-1f0e1700-65d4-11e9-8cc6-27425f5e25ed.png)

### Chrome / Firefox

[![Sidebar in Chrome or Firefox](img/e8a9889d0bf2c333eee26daa166682e7.png)](https://user-images.githubusercontent.com/17884966/56606420-2c2b0600-65d4-11e9-9ad3-a906c6df2710.png)

最值得注意的问题是按钮没有 Chrome / Firefox 宽，菜单弹出得更远。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2537)

*   [@nickytonline](https://dev.to/nickytonline) 发起了一个讨论，考虑为主题风格使用一个`SASS` mixin。那里有一个很好的解决方案，值得一试。谢谢尼克。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 考虑使用 SASS mixin 作为主题风格 #2539](https://github.com/thepracticaldev/dev.to/issues/2539) 

[![nickytonline avatar](img/849ad90a82f294a93718c2c4da688f82.png)](https://github.com/nickytonline) **[nickytonline](https://github.com/nickytonline)** posted on [<time datetime="2019-04-24T02:17:30Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2539)

**您的功能请求是否与某个问题相关？请描述一下。**不是用户问题，更多的是为了更好的开发者体验的建议。 [Fallbacks 可以被遗忘](https://github.com/thepracticaldev/dev.to/pull/2538#discussion_r277981949)没有人比他们更喜欢打字。<g-emoji class="g-emoji" alias="wink" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f609.png">😉</g-emoji>

目前，如果添加主题化样式，你可以这样写

```
  box-shadow: $bold-shadow; /* the fallback */
  box-shadow: var(--theme-container-box-shadow, $bold-shadow); /* modern css */
```

Enter fullscreen mode Exit fullscreen mode

**描述你想要的解决方案**考虑使用 SASS mixin 来处理主题化风格。

```
@mixin themeable(
  $property,
  $cssVariable,
  $fallback) {

  #{$property}: #{$fallback};
  #{$property}: var(--#{$cssVariable}, #{$fallback});
}

.some-class {
 [@include](https://dev.to/include) themeable(box-shadow, theme-container-box-shadow, $bold-shadow)
}
```

Enter fullscreen mode Exit fullscreen mode

会产生

```
.some-class {
  box-shadow: $bold-shadow;
  box-shadow: var(--theme-container-box-shadow, $bold-shadow);
}
```

Enter fullscreen mode Exit fullscreen mode

**描述你考虑过的替代方案**不适用

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2539)

*   [@rhymes](https://dev.to/rhymes) 报告了一个错误，阅读列表中的“最近评论活动”部分有一点不符合风格。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 近期评论活动造型有点脱 #2546](https://github.com/thepracticaldev/dev.to/issues/2546) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-24T20:52:39Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2546)

**描述 bug**

我认为“最近评论活动”的风格有点不对

**重现**重现行为的步骤:

1.  保存带评论的文章
2.  转到'[https://dev.to/readinglist?v=comments](https://dev.to/readinglist?v=comments)
3.  看到问题了吗

**预期行为**

我认为文章标题、“查看评论树”部分和实际评论之间需要一些间隔。

**截图**

[![Screenshot_2019-04-24 The DEV Community](img/5490c091f31261d386e6a6f668fe7dbd.png)](https://user-images.githubusercontent.com/146201/56692772-3ab41300-66e3-11e9-9d9a-f734c3d0ac8b.png)

(忽略中间的搜索栏，这只是截屏工具的问题)

**桌面(请填写以下信息):**

*   操作系统:macOS 10.14.4
*   浏览器 Firefox
*   67.0b12 版

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2546)

*   [@jrc86](https://dev.to/jrc86) 请求了一个功能，为阅读列表设置提醒。这是一个有趣的想法；谢谢， [@jrc86](https://dev.to/jrc86) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 我的读书清单提醒 #2547](https://github.com/thepracticaldev/dev.to/issues/2547) 

[![JRC86 avatar](img/6c4f5a1b43db66592847b856b0679a4a.png)](https://github.com/JRC86) **[JRC86](https://github.com/JRC86)** posted on [<time datetime="2019-04-24T21:00:08Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2547)

**您的功能请求是否与某个问题相关？请描述一下。**我喜欢保存文章以便以后阅读的想法，但是列表增长很快，我跟不上。保存文章的原因是因为它们很有趣，我当时可能没有时间通读。

**描述你想要的解决方案**我提醒，它像通知一样，问我是否有(x 分钟)空闲时间。点击那个提醒会把我带到一篇已保存的文章，估计阅读时间为 x 分钟。我确实有时会有 x 分钟的时间，但如果手动访问 dev.to **描述你考虑过的替代方案**增强我的自律，我可能会将更多的文章保存到列表中。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2547)

*   [@andrewbrown](https://dev.to/andrewbrown) 开启了一个关于如何让文章系列更有用的讨论。感谢 [@andrewbrown](https://dev.to/andrewbrown) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 做系列文章有用的 #2558](https://github.com/thepracticaldev/dev.to/issues/2558) 

[![omenking avatar](img/c5b3096d42a99b0bcad72567d37cd4a0.png)](https://github.com/omenking) **[omenking](https://github.com/omenking)** posted on [<time datetime="2019-04-25T19:44:38Z">Apr 25, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2558)

**您的功能请求是否与某个问题相关？请描述一下。**

这就是数列的工作原理，它们是一系列的点。[![Screen Shot 2019-04-25 at 3 42 39 PM](img/a415dc7bfa1e3b002d66d7f9a0965ef7.png)](https://user-images.githubusercontent.com/7776/56763585-cb850000-6770-11e9-8a56-c09b8aaaaa11.png)

**描述您希望**在我用红色突出显示的左侧看到的解决方案。这将是更理想的，因为我可以发布一个完整的课程专门开发到 [![Screen Shot 2019-04-25 at 3 40 41 PM](img/ccba6f9e8635c226abba53b42b3f9d4d.png)](https://user-images.githubusercontent.com/7776/56763633-e192c080-6770-11e9-993d-2682e4ba9adc.png)

**描述你考虑过的替代方案**

**附加上下文**

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2558)

*   [@mariocsee](https://dev.to/mariocsee) 开了一个概览问题，追踪重构液态标签。感谢你的组织，马里奥！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 液体标签渲染重构跟踪器 #2559](https://github.com/thepracticaldev/dev.to/issues/2559) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-04-25T20:07:40Z">Apr 25, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2559)

**描述 bug** 大多数/所有 liquid 标签通过 markdown 解析器呈现，与文章/评论在保存和呈现之前经历的准备步骤相同。一些代码、HTML 等内容。在过程中被移除，并创建方法来避开解析(通常使用 Regex)。通过使用视图来呈现 liquid 标签，我们将不再需要这样的方法，并且可以在 xyz_tag.rb 中导入和处理有助于安全性的方法。

**预期行为**改变 liquid 标签的呈现方式——从通过 markdown 解析器和与文章/评论呈现方式相同的过程，到在 html.erb 视图上单独呈现。有几个开放的液体标签相关的问题将会并且可能会通过这个重构得到解决。

**液体标签跟踪器**

*   [x]开发到注释#2554 已合并
*   [x]文章开发/文章#2619 已合并
*   [x]开发给用户#2621
*   [x]开发至标签#2641 已合并
*   [x] StackBlitz #2664 已合并
*   [x]开发播客#2644
*   [x]推特#2721
*   [x]手淫#2723
*   [x] Youtube 和 Vimeo #2728
*   [x]回复它
*   [x] JS 小提琴
*   [x]代码手册
*   [x]代码笔
*   [x] Instagram
*   [x]扬声器面板
*   [x]声音云
*   [x] Spotify
*   [x]博客

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2559)

*   [@rhymes](https://dev.to/rhymes) 开了一个问题，提出了这样一个问题:“赛欧实际上被使用了吗？”一个值得问的好问题！更多细节请见本期。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 赛欧真的被利用了吗？ #2573](https://github.com/thepracticaldev/dev.to/issues/2573) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-26T10:00:13Z">Apr 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2573)

我注意到 [sail](https://rubygems.org/gems/sail) 列在 gem 文件中，但它从未实际使用过。

还有一张桌子:

[https://github . com/thepractical dev/dev . to/blob/a711e 2246 e 7 F4 ca 87 afcd 247 a 63 D5 f 0 f 773836 f 8/db/schema . Rb # L681-L689](https://github.com/thepracticaldev/dev.to/blob/a711e2246e7f4ca87afcd247a63d5f0f773836f8/db/schema.rb#L681-L689)

但是该路由没有被激活，也没有任何其他代码引用它。

我检查了 git 历史记录，发现了这三个提交:

1.  赛欧取代触发器:[https://github . com/thepractical dev/dev . to/commit/21870 e 805 a5 D1 e 11790 bfc 356 B4 C5 C4 ea 817 ba 98](https://github.com/thepracticaldev/dev.to/commit/21870e805a5d1e11790bfc356b4c5c4ea817ba98)发自【https://github.com/thepracticaldev/dev.to/pull/841】T2
2.  恢复之前的提交:[https://github . com/thepractical dev/dev . to/commit/06 E0 cbabbf 9 a7f 0517 cabb 532 de 5 ebdd 4d 66 c](https://github.com/thepracticaldev/dev.to/commit/06e0cbabbbf9a7f0517cabb532de3e5ebdd4d66c)
3.  从代码中移除触发器:[https://github . com/thepractical dev/dev . to/commit/88 CD 4c 13 f 31 a1 FD 76002529 c 78835018 a71d 618 f](https://github.com/thepracticaldev/dev.to/commit/88cd4c13f31a1fd76002529c78835018a71d618f)

概括一下:目前在“触发器”的代码中有一些引用，尽管 gem 不存在(主要是在跳过的测试中，这些测试也可能被删除)，并且除了 gem 之外，数据库中还有“sail”表。

如果这些是从未使用过的功能的残余，我很乐意提交一份 PR 来清理它，但也许 DEV 正在外部使用 Sail 表。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2573)

*   [@lightalloy](https://dev.to/lightalloy) 用`DelayedJob`报告了一个错误，内容导出作业失败。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 导出内容时出现延迟作业错误 #2580](https://github.com/thepracticaldev/dev.to/issues/2580) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-26T13:25:35Z">Apr 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2580)

有时`ExportJob`会出现以下错误:

```
--- !ruby/object:ActiveJob::QueueAdapters::DelayedJobAdapter::JobWrapper
    job_data:
      job_class: ExportContentJob
      job_id: some_id
      provider_job_id: 
      queue_name: export_content
      priority: 
      arguments:
      - id
      executions: 0
      locale: en

Last Error
    Toggle full message

    undefined method `new' for Articles:Module
    /app/app/services/exporter/service.rb:21:in `block in export'
    /app/app/services/exporter/service.rb:20:in `each'
    /app/app/services/exporter/service.rb:20:in `export'
    /app/app/jobs/export_content_job.rb:6:in `perform' 
```

这可能与#2031 有关在`Exporter::Service`里面有一个对`Articles`的引用，它被解析为一个`Articles`模块。不过应该会解析到`Exporter::Articles`。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2580)

# 开发-iOS

我们最近没有任何新的问题或 PRs 合并。请随意查看 [iOS 回购](https://github.com/thepracticaldev/dev-ios)，或者在 App Store 下载[我们的 iOS 应用。](https://itunes.apple.com/us/app/dev-community/id1439094790)

# DEV-Android

我们在过去的一周刚刚发布，我们已经有了一些问题和讨论！

## 新问题/讨论

*   [@maestromac](https://dev.to/maestromac) 请求了一项功能来为应用程序启用推送通知:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 推送通知 #10](https://github.com/thepracticaldev/DEV-Android/issues/10) 

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2019-04-22T21:35:56Z">Apr 22, 2019</time>](https://github.com/thepracticaldev/DEV-Android/issues/10)

**您的功能请求是否与某个问题相关？请描述一下。**该应用程序目前不会通知用户他们已收到通知。

**描述您想要的解决方案**这似乎需要结合 JavaScript 注入和第三方服务，如 Firebase。它还应该允许用户更改他们通知首选项。

**描述你考虑过的替代方案**不适用

**附加上下文** [开发人员的连接](https://dev.to/connect)的消息通知应该与普通通知分开处理。

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/issues/10)

*   [@maestromac](https://dev.to/maestromac) 报告了一个 Web Share API 不工作的 bug。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Web 共享 API 不工作 #11](https://github.com/thepracticaldev/DEV-Android/issues/11) 

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2019-04-22T22:32:10Z">Apr 22, 2019</time>](https://github.com/thepracticaldev/DEV-Android/issues/11)

**描述 bug** 我们使用 Web Share API 来简化分享。它目前可以在浏览器上工作，但不能在应用程序中工作。

**重现**重现行为的步骤:

1.  打开应用程序。
2.  点击任何文章。
3.  点击右下角的三点。
4.  Web 共享 API 未激活。

预期行为对你预期会发生的事情的清晰简明的描述。

**截图**

**左【WebShare 不工作时。**右**:当是**

[![](img/0ec2329da183a5807b952557abb456ea.png) ](https://user-images.githubusercontent.com/15793250/56536437-0fd38e80-652c-11e9-8bbf-f05a207be6ed.jpg) [ ![](img/797cb91008eca871bc46bccfa813f9b5.png)](https://user-images.githubusercontent.com/15793250/56536467-237ef500-652c-11e9-8b28-83415ca03422.jpg)

**智能手机(请填写以下信息):**

*   设备:一加 6T
*   OS: 9.0 "Pie"
*   版本:1.0

**附加上下文**[拉动请求](https://github.com/thepracticaldev/dev.to/pull/1524)进行实施

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/issues/11)

*   我报告了一个问题，如果你有一个非 Chrome 浏览器作为你的默认 Android 浏览器，登录不起作用。这很可能是因为目前应用程序链接的处理方式。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 用火狐作为默认浏览器登录不起作用 #12](https://github.com/thepracticaldev/DEV-Android/issues/12) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-04-23T01:09:32Z">Apr 23, 2019</time>](https://github.com/thepracticaldev/DEV-Android/issues/12)

## 当前行为

如果我的默认浏览器是 Firefox，并且我试图登录 DEV Android 应用程序，它会在实际的 Firefox 应用程序中打开一个等待打开的新标签。

## 预期的行为

我应该能够用任何浏览器正确地通过 OAuth 登录流程。

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/issues/12)

*   [@jess](https://dev.to/jess) 报告了自适应图标的问题。也已经有公关开了！🎉

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新适应性图标 #13](https://github.com/thepracticaldev/DEV-Android/issues/13) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-04-24T14:11:59Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/DEV-Android/issues/13)

**描述向 [yo@dev.to](mailto:yo@dev.to) 报告的 bug** :

发现一个错误，你的自适应图标有一个不好的背景！

理想情况下，你的背景应该是全黑的，前景应该是透明的白色开发文本。否则，当我在主屏幕上晃动应用程序时，会出现附加的情况，哈哈

**截图** [![](img/0d8ca274c1244169c0e075a63567936c.png)](https://camo.githubusercontent.com/25a32c250b9dfdd3a44babb6e9d1edf122c71d88/68747470733a2f2f636c2e6c792f3661653237386537653662332f646f776e6c6f61642f496d616765253230323031392d30342d3234253230617425323031302e31312e3338253230414d2e706e67)

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/issues/13)

*   [@c33s](https://dev.to/c33s) 要求将应用提交给`f-droid`，这是 Google Play 商店的一个开源替代品。谢谢， [@c33s](https://dev.to/c33s) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 把你的 app 添加到 f-droid.org# 14](https://github.com/thepracticaldev/DEV-Android/issues/14)

[![c33s avatar](img/fcd8f5db4b9a53ce4c935d64f8e83d8d.png)](https://github.com/c33s) **[c33s](https://github.com/c33s)** posted on [<time datetime="2019-04-24T14:45:49Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/DEV-Android/issues/14)

f-droid 是 playstore 的开源替代产品。许多不想安装 google play 服务或不喜欢开源软件的用户使用它。

[https://F-Droid . org/en/docs/Submitting _ to _ F-Droid _ Quick _ Start _ Guide/](https://f-droid.org/en/docs/Submitting_to_F-Droid_Quick_Start_Guide/)

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/issues/14)

*   [@siddeshpillai](https://dev.to/siddeshpillai) 报告了一个问题，如果您的阅读列表为空，文本本身会被侧边栏的边框截断或剪掉。谢谢， [@siddeshpillai](https://dev.to/siddeshpillai) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 标题“我的阅读列表(空的)夹在导航菜单上 #17](https://github.com/thepracticaldev/DEV-Android/issues/17) 

[![siddeshpillai avatar](img/9440cc6b1d25c4a6a0cebe698d44eca1.png)](https://github.com/siddeshpillai) **[siddeshpillai](https://github.com/siddeshpillai)** posted on [<time datetime="2019-04-24T18:38:58Z">Apr 24, 2019</time>](https://github.com/thepracticaldev/DEV-Android/issues/17)

**UI bug** 我的阅读列表(空)文本被夹在 Pixel 2 的导航菜单上。

**再现步骤**

1.  打开应用程序
2.  点击左上角的汉堡按钮或从屏幕左边缘滑动打开导航菜单
3.  参见截图部分

**预期行为**文本显示流向下一行，或者根据设备的比例因子设置文本像素大小。

**截图** [![Screenshot_20190424-112229](img/68e01371d352d646c23cb406454371bc.png)](https://user-images.githubusercontent.com/4101783/56684719-30632c00-6685-11e9-958b-a3e78cfb7edb.jpg)

**智能手机(请填写以下信息):**

*   设备:像素 2
*   OS: 9.0 "Pie"

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/issues/17)

*   [@bhupesh](https://dev.to/bhupesh) 报告了一个无法上传个人资料图片的问题。谢谢， [@bhupesh](https://dev.to/bhupesh) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 选择文件选项不起作用 #18](https://github.com/thepracticaldev/DEV-Android/issues/18) 

[![Bhupesh-V avatar](img/ee5f493a912a2c334f3d478a58daef3e.png)](https://github.com/Bhupesh-V) **[Bhupesh-V](https://github.com/Bhupesh-V)** posted on [<time datetime="2019-04-25T16:53:26Z">Apr 25, 2019</time>](https://github.com/thepracticaldev/DEV-Android/issues/18)

**描述 bug**app 上更新个人资料图片的选项没有反应。

**重现**重现行为的步骤:

1.  转到“设置/个人资料图像”
2.  点击“选择文件”
3.  参见错误

**预期行为**按钮没有响应。

**智能手机(请填写以下信息):**

*   设备:[亚马逊的 Tenor G]
*   OS: [e.g. 8.0 "Oreo"]

[View on GitHub](https://github.com/thepracticaldev/DEV-Android/issues/18)

这星期到此为止！请在周末的某个时候继续关注下周的版本。