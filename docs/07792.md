# dev.to Repo 回顾过去三周的情况

> 原文：<https://dev.to/devteam/dev-to-repo-recap-from-the-past-three-weeks-5f1g>

欢迎回到另一个回购回顾，在这里我们(通常)涵盖上周对 [dev.to 库](https://github.com/thepracticaldev/dev.to)和[iOS 回购](https://github.com/thepracticaldev/dev-ios)的贡献。这一期将覆盖三周，从 3 月 16 日到 4 月 5 日。

# 特性

*   夜晚模式在公测中正式出来了，还有改变帖子的字体！您可以在您的设置中更改它:[https://dev.to/settings/misc](https://dev.to/settings/misc)感谢[@本](https://dev.to/ben)让它出门！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 给用户风格配置添加适当的 css 变量 #2105](https://github.com/thepracticaldev/dev.to/pull/2105) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-18T18:21:18Z">Mar 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2105)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这将使用 CSS 变量呈现正确的夜晚主题。这目前在`application.html.erb`中采用硬编码的主题信息，但将来可以修改以采用变量。

[![](img/ea4a4a61625f9297d5f790d3fa60bef9.png)](https://camo.githubusercontent.com/f98498210251f6b06181dc8d845185333ecf1e0c/68747470733a2f2f636c2e6c792f3232356366336230313661362f496d616765253230323031392d30332d31382532306174253230322e32302e3436253230504d2e706e67)

还增加了另一个字体选项:`comic sans`。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2105)

*   感谢 [@picocreator](https://dev.to/picocreator) 让我们的 Docker 构建一步到位！PR 中的更多详细信息:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 功能:docker-run.sh 脚本+ docker 容器构建 #1844](https://github.com/thepracticaldev/dev.to/pull/1844) 

[![PicoCreator avatar](img/7d9c85b16a139dfe22b8918d388ce270.png)](https://github.com/PicoCreator) **[PicoCreator](https://github.com/PicoCreator)** posted on [<time datetime="2019-02-22T09:09:46Z">Feb 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/1844)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

一个 bash 脚本，帮助快速设置开发或演示环境

```
bash-3.2$ ./docker-run.sh 
```

Enter fullscreen mode Exit fullscreen mode

```
#---
#
# This script will perform the following steps ... 
#
# 1) Stop and remove any docker container with the name 'dev-to-postgres' and 'dev-to'
# 2) Reset any storage directories if RUN_MODE starts with 'RESET-'
# 3) Build the dev.to docker image, with the name of 'dev-to:dev' or 'dev-to:demo'
# 4) Deploy the postgres container, mounting '_docker-storage/postgres' with the name 'dev-to-postgres'
# 5) Deploy the dev-to container, with the name of 'dev-to-app', and sets up its port to 3000
#
# To run this script properly, execute with the following (inside the dev.to repository folder)...
# './docker-run.sh [RUN_MODE] [Additional docker envrionment arguments]'
#
# Alternatively to run this script in 'interactive mode' simply run
# './docker-run.sh INTERACTIVE-DEMO'
#
#---
#---
#
# RUN_MODE can either be the following
#
# - 'DEV'  : Start up the container into bash, with a quick start guide
# - 'DEMO' : Start up the container, and run dev.to (requries ALGOLIA environment variables)
# - 'RESET-DEV'   : Resets postgresql and upload data directory for a clean deployment, before running as DEV mode
# - 'RESET-DEMO'  : Resets postgresql and upload data directory for a clean deployment, before running as DEMO mode
# - 'INTERACTIVE-DEMO' : Runs this script in 'interactive' mode to setup the 'DEMO'
#
# So for example to run a development container in bash its simply
# './docker-run.sh DEV'
#
# To run a simple demo, with some dummy data (replace <?> with the actual keys)
# './docker-run.sh DEMO -e ALGOLIASEARCH_APPLICATION_ID=<?> -e ALGOLIASEARCH_SEARCH_ONLY_KEY=<?> -e ALGOLIASEARCH_API_KEY=<?>'
#
# Finally to run a working demo, you will need to provide either...
# './docker-run.sh .... -e GITHUB_KEY=<?> -e GITHUB_SECRET=<?> -e GITHUB_TOKEN=<?>
#
# And / Or ...
# './docker-run.sh .... -e TWITTER_ACCESS_TOKEN=<?> -e TWITTER_ACCESS_TOKEN_SECRET=<?> -e TWITTER_KEY=<?> -e TWITTER_SECRET=<?>
#
# Note that all of this can also be configured via ENVIRONMENT variables prior to running the script
#
#---
```

Enter fullscreen mode Exit fullscreen mode

并使用 docker 进行部署。包括在部署前进行重置的选项。

此处提供了可选的上下文信息:[单指令 https://dev.to/uilicious/adopt-your-own-devto-几乎-1c04](https://dev.to/uilicious/adopt-your-own-devto----with-a-single-command-almost-1c04)

## 需要指点...

如果有人能指导我如何在“生产”模式下运行 dev.to，这将有助于提高 docker 容器的整体性能

## 添加到文档中？

*   docker hub 的自述文件示例:[https://github . com/uilicious/dev . to-docker/blob/feature/docker-run-script/docker hub-readme . MD](https://github.com/uilicious/dev.to-docker/blob/feature/docker-run-script/dockerhub-readme.md)
*   [https://cloud . docker . com/u/uilicious/repository/docker/uilicious/dev . to](https://cloud.docker.com/u/uilicious/repository/docker/uilicious/dev.to)
*   我可以修改 readme 如果这个看起来不错？

## 什么 gif 最能描述这次公关

[![quick demo](img/7e0e38127a1885f0aace4f459bf582c7.png)](https://camo.githubusercontent.com/8bfe439f41fd2a49537c35ef8a372afa1795f462/68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f6e686c7262366535326b6374787434667a726d762e676966)

## 什么 gif 最能描述它给你的感觉？

[![how i feel](img/b8339a14cbc8faacf60313fd71081ba7.png)](https://camo.githubusercontent.com/4d650253e40f7666e135c1314b84586fe3e17b52/68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f376977756e6262796f76336d66746f77746867632e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/1844)

*   当你写一篇新文章时，你现在可以通过参数添加预填充的 markdown。`https://dev.to/new`！将来会有更多关于这个特性的细节！公关由[@本](https://dev.to/ben)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/新增# 2108](https://github.com/thepracticaldev/dev.to/pull/2108)

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-18T22:27:06Z">Mar 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2108)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这个特性允许用户提供一个类似于`dev.to/new?prefill=A BUNCH OF MARKDOWN`的 url 来提供任意的模板。这类似于标签预填充模板。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2108)

*   我们现在对你的帖子有了一些本地视图跟踪。这有助于我们更容易地显示趋势。PR by [@ben](https://dev.to/ben)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加有机页面浏览量总和并修改 SEO boost ables# 2109](https://github.com/thepracticaldev/dev.to/pull/2109)

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-18T23:07:24Z">Mar 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2109)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这允许我们存储关于有机页面视图(最近和所有时间)的数据，并以这种方式显示趋势。用于边栏，以帮助维护搜索引擎优化的网页已经做得很好。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2109)

*   多亏了 [@willamesoares](https://dev.to/willamesoares) ，评论框/文本区现在可以调整大小了！尽情放大。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 允许用户调整编辑评论文本区元素的大小 #1866](https://github.com/thepracticaldev/dev.to/pull/1866) 

[![willamesoares avatar](img/8141b96b7e754561d906f1247fd6b891.png)](https://github.com/willamesoares) **[willamesoares](https://github.com/willamesoares)** posted on [<time datetime="2019-02-24T22:36:01Z">Feb 24, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/1866)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

根据投诉，这允许用户在编辑评论时调整文本区域元素的大小。

## 相关车票&单据

#1554

## 手机&桌面截图/录音(如有 UI 改动)

[![edit_comment](img/9ceed789218e49f2e866d1ac2a084d82.png)](https://user-images.githubusercontent.com/6568078/53306389-3dbd8f00-386b-11e9-969a-ed170ad49293.gif)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/1866)

*   [@davefollett](https://dev.to/davefollett) 标签移除文本区更改:用改进的

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 标签移除 textarea 修改:用改进的…   #2063](https://github.com/thepracticaldev/dev.to/pull/2063) 替换占位符文本

[![davefollett avatar](img/aeaf8d919891954049375b3f8d386d1e.png)](https://github.com/davefollett) **[davefollett](https://github.com/davefollett)** posted on [<time datetime="2019-03-14T03:20:11Z">Mar 14, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2063)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

标签移除文本区域更改:

*   ~~用改进的示例文本替换了占位符文本~~改进了占位符文本，以明确版主只需提供一个理由。
*   增加高度，以便默认显示所有文本

## 相关车票&单据

第 2001 号决议

## 手机&桌面截图/录音(如有 UI 改动)

[![Screenshot_20190313_231631](img/883342bfad18558667c9dc521da28de6.png)](https://user-images.githubusercontent.com/6683520/54328906-0c242200-45e6-11e9-99ac-7a76ec46a1fb.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2063)

*   [@ben](https://dev.to/ben) 在主列表、通知和聊天中添加主题

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 给主列表、通知和聊天添加主题 #2119](https://github.com/thepracticaldev/dev.to/pull/2119) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-20T01:53:21Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2119)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

更多主题善良！

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2119)

*   一个小特性，但是我们现在通过一个环境变量来设置站点标志。PR by [@ben](https://dev.to/ben)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 通过环境变量  #2123](https://github.com/thepracticaldev/dev.to/pull/2123) 增加设置站点标志的能力

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-20T14:46:03Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2123)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这使得管理员可以为特殊事件以及平台的通用实例设置新的徽标。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2123)

*   感谢[@韵](https://dev.to/rhymes)在图片上传失败时增加了更清晰的错误提示！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 处理图片上传错误 #2018](https://github.com/thepracticaldev/dev.to/pull/2018) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-10T14:56:28Z">Mar 10, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2018)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

目前，当图像上传导致错误时，服务器“中断”，产生 HTTP 500 未处理的错误，并且对于评论编辑器和编辑器 v1，向用户显示一般的“无效文件”,而对于编辑器 v2，什么也不显示。

在此 PR 中，我添加/更改了以下内容:

*   一个适当的错误处理服务器端(一个图像上传错误不应该导致 HTTP 500，因为错误是好的，它是客户端发送了一些错误)

*   与客户端无关的错误确实应该生成 HTTP 500

*   CarrierWave 返回的与客户端错误相关的消息直接显示给用户(我检查了 CarrierWave 自己的 [locale/en.yml](https://github.com/carrierwaveuploader/carrierwave/blob/master/lib/carrierwave/locale/en.yml) ,它们看起来可以被用户使用

*   由 CarrierWave 返回的与服务器错误相关的消息仍然被处理，但是一个通用的“服务器错误已经发生”被发送到客户端(这些也应该被发送到错误跟踪器吗？)

*   我在客户端添加了错误处理

注意:我对在编辑器 v2 中显示上传错误的方式不是很有信心，让我知道它是否应该以不同的方式显示，或者它是否应该有一个更好的样式。

我为这三个案例添加了屏幕录像

## 相关车票&单据

关闭#936，关闭#2008

## 手机&桌面截图/录音(如有 UI 改动)

**编辑 v1**

[![editor-v1](img/ff8ee039608ec6456c2a5d0b3b7e2e06.png)](https://user-images.githubusercontent.com/146201/54086770-c0a31700-434c-11e9-960c-86df017ae158.gif)

**编辑 v2**

[![editor-v2](img/ab8a8dd42110b28635fc60a876c26a49.png)](https://user-images.githubusercontent.com/146201/54086785-d9133180-434c-11e9-94c7-8d7988fc9d39.gif)

**评论编辑**

[![comment-editor](img/1484583e56a721c03558ab480ae118a5.png)](https://user-images.githubusercontent.com/146201/54086792-e7f9e400-434c-11e9-939a-e5701dbe01f6.gif)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2018)

*   @dabrorius 在 dashboard 中添加了一个关注的组织列表。谢谢， [@dabrorius](https://dev.to/dabrorius) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将关注的组织列表添加到仪表板 #2157](https://github.com/thepracticaldev/dev.to/pull/2157) 

[![dabrorius avatar](img/08546437ad0d94cda8506678b76460b1.png)](https://github.com/dabrorius) **[dabrorius](https://github.com/dabrorius)** posted on [<time datetime="2019-03-21T13:38:23Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2157)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将关注的组织列表添加到用户的仪表板。

## 相关车票&单据

#1958

## 手机&桌面截图/录音(如有 UI 改动)

[![Screen Shot 2019-03-20 at 15 53 47](img/8731b5ca4adccc0eacb742528ec69e12.png)](https://user-images.githubusercontent.com/690113/54755595-8bb58080-4be6-11e9-8d03-b6100b05604c.png)

[![Screen Shot 2019-03-21 at 14 36 14](img/2b275dd14794ffc61a08536e85237735.png)](https://user-images.githubusercontent.com/690113/54755665-b56ea780-4be6-11e9-851a-77aaaf4cc34c.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/8c5de2eaa1e08e625d4b1eb75eb102db.png)](https://camo.githubusercontent.com/150ba2588c93ebbd089eea8570282a87bb9219df/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6d7875504f477531335173726d2f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2157)

*   我们现在在左侧放了一些商店横幅，以防你想展示自己的风格。 PR by [@ben](https://dev.to/ben)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在左侧栏  #2162](https://github.com/thepracticaldev/dev.to/pull/2162) 添加开发商店显示区域

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-21T18:57:39Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2162)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

增加了在左侧边栏底部放置商店和其他主题日内容的功能

[![Screen Shot 2019-03-21 at 2 55 56 PM](img/a8b9e161ff1bdff3f2d6d7019842ea6e.png)](https://user-images.githubusercontent.com/3102842/54777830-8efe3b80-4be9-11e9-9bc2-0fd8f2a685de.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2162)

*   多亏了 [@jess](https://dev.to/jess) ，我们现在可以通过内部工具合并账户了！如果你有一个重复帐户的问题，随时发送电子邮件给 [yo@dev.to](mailto:yo@dev.to) 。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 合并用户并赋予亲用户可信角色 #2176](https://github.com/thepracticaldev/dev.to/pull/2176) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-03-22T17:51:01Z">Mar 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2176)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

*   自动给予“专业”用户信任的角色。我相信专业的东西将很快被移出这个地区，但我认为这是有意义的。
*   将合并用户功能添加到/internal。
*   如果要删除的帐户有两个身份，则阻止合并(可能表示该帐户不应被删除)

[![](img/b6b1402a22df26c5359a05ef2ac9332d.png)](https://camo.githubusercontent.com/d89ca0ca4d9e178615ac860c39df7c9d885f9969/68747470733a2f2f636c2e6c792f3265626235396538353339372f646f776e6c6f61642f496d616765253230323031392d30332d32322532306174253230312e35302e3239253230504d2e706e67)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2176)

*   [@ben](https://dev.to/ben) 用几个新的推荐标签更新了我们的 onboarding。请查看以下内容:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加几个入职推荐标签 #2217](https://github.com/thepracticaldev/dev.to/pull/2217) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-26T14:11:41Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2217)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这给入职流程添加了一些流行的标签。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2217)

*   多亏了 [@mariocsee](https://dev.to/mariocsee) ，您现在可以在 Connect chat 中阻止或轻松举报滥用！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在开发连接中拦截/报告滥用 #2074](https://github.com/thepracticaldev/dev.to/pull/2074) 

[![mariocsee avatar](img/f01c3ff38b82d1fd49d8d86f5ae083a7.png)](https://github.com/mariocsee) **[mariocsee](https://github.com/mariocsee)** posted on [<time datetime="2019-03-15T18:57:06Z">Mar 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2074)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]功能

## 描述

为 DEV Connect 添加了阻止和报告滥用功能，以防止滥用。用户可以保持收件箱打开，被阻止的用户无法重新打开与他们的聊天。

`blocked`现在是`ChatChannel` `status`的一个选项。这阻止了`ChatChannel`在`/connect`中被渲染，并且不允许`status`被更改为`active`。目前，没有办法在用户端解除对某人`/connect`的封锁。

## 相关车票&单据

从#1563 继续关闭

## 手机&桌面截图/录音(如有 UI 改动)

### 挡住

[![blocking](img/c9898443e79524540f5b2826b22b9f90.png)](https://user-images.githubusercontent.com/13403332/54554262-73492a00-498a-11e9-92b9-8246bd3c7bc0.gif)

### 举报谩骂

[![report-abuse](img/976fa17df029836ab1baf4d1d7d03e0f.png)](https://user-images.githubusercontent.com/13403332/54554273-78a67480-498a-11e9-800c-5d7ded95b803.gif)

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2074)

*   对于愚人节，我们试图做一些事情，但最终有点失控...这是令人不快的公关，以及相关的帖子:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 制作漫画无内定 #2257](https://github.com/thepracticaldev/dev.to/pull/2257) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-01T11:13:13Z">Apr 01, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2257)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

做出正确的选择

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2257)[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## 漫画 Sans 现在是默认阅读字体

### 开发团队的 Ben Hal pern 1919 年 4 月 1 日 2 分钟阅读

#meta #webdev](/devteam/comic-sans-is-now-the-default-reading-font-2n6g)

*   我们现在有了用于分析的 API 端点！这目前只对专业会员开放(更多细节在后面)。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加 API 分析端点 #2293](https://github.com/thepracticaldev/dev.to/pull/2293) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-04-02T21:13:25Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2293)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]功能

## 描述

这通过 API for stats 添加了分析端点！这目前只提供给专业会员。也是组织一部分的专业会员也可以通过 API 查看他们各自组织的统计数据。

目前有三个端点:

*   给你所有帖子的统计数据的摘要，
*   `/api/analytics/past_day`为您提供过去 24 小时的数据，以及
*   `/api/analytics/historical`，它根据特定的时间范围为您提供每天的数据快照。

你需要通过 https://dev.to/settings/account 的[生成一个 API 令牌来授权你自己。如果您想查看您组织的数据，您可以添加`organization_id`参数。这里有一个端点示例:`https://dev.to/api/analytics/totals?api_token=blahblahblah&organization_id=9001`](https://dev.to/settings/account)

对于`/historical`，必须至少添加一个开始日期参数`start`。结束日期参数`end`是可选的，省略它将给出到当前日期为止的所有数据。两个日期参数都必须采用`yyyy-mm-dd`的格式(例如。2019-03-29)示例端点:

```
https://dev.to/api/analytics/historical?api_token=blahblahblah&start=2019-03-26
https://dev.to/api/analytics/historical?api_token=blahblahblah&start=2019-03-01&end=2019-03-14 
```

## 添加到文档中？

还没有，但很快会添加到 docs.dev.to！

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2293)

*   我们还有一个仪表板，用于对上述专业会员进行更详细的分析:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用 Org Pro 仪表盘视图 #2060](https://github.com/thepracticaldev/dev.to/pull/2060) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-03-13T22:00:32Z">Mar 13, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2060)

此请购单启用组织专业仪表板视图。我决定把重构和提供更多数据的工作留给下一次公关。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2060)

*   受信任的社区成员现在可以为帖子建议社会副本！PR by [@ben](https://dev.to/ben) :

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 为可信用户添加建议社交副本的能力 #2306](https://github.com/thepracticaldev/dev.to/pull/2306) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-04T16:03:06Z">Apr 04, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2306)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

该功能允许社区成员为发布到社交账户的帖子提供 Twitter 副本。这是最低可行版本。边缘可能有些粗糙。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2306)

*   [@bolariinwa](https://dev.to/bolariinwa) 在 Markdown 中增加了对 HTML `<mark>`元素的支持。这意味着你现在可以高亮显示你喜欢的东西。谢谢@bolariinwa！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在 markdown   #2089](https://github.com/thepracticaldev/dev.to/pull/2089) 中增加对标记元素的支持

[![bolariin avatar](img/a5411278d9eefe6f01d3157b0ad4f809.png)](https://github.com/bolariin) **[bolariin](https://github.com/bolariin)** posted on [<time datetime="2019-03-18T00:52:08Z">Mar 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2089)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [X]错误修复
*   [ ]文档更新

## 描述

`<mark>`有助于作者突出显示重要文本。根据 [@link2twenty](https://dev.to/link2twenty) 的建议，我更新了`rendered_markdown_scrubber.rb`的`tags`列表。

## 相关车票&单据

发行号是#1870。

## 手机&桌面截图/录音(如有 UI 改动)

[![Text Highlight using mark Element](img/fcad99b6fa830a99297a0a797465137e.png)](https://user-images.githubusercontent.com/24629960/54500738-5b27cb00-48f6-11e9-8572-b27e2ae9d5af.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [X]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2089)

*   https://dev.to/videos 出局了！这是我们在 dev.to 上所有原生上传视频的登录页面。我们将很快做出更多改进。公关由[@杰斯](https://dev.to/jess):

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

# Bug 修复/其他贡献

*   感谢 [@jess](https://dev.to/jess) 加入侦察兵配置和宝石在！看到我们收到的电子邮件中的一些初步数据，我个人非常兴奋。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加斥候配置和宝石 #2219](https://github.com/thepracticaldev/dev.to/pull/2219) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-03-26T16:10:54Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2219)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

*   增加侦察宝石和配置文件

[![](img/8cc1514fbcb6141ff06c90be86309fa3.png)](https://camo.githubusercontent.com/49d6b26773deb89f50a5901c1c239b9b546b62d1/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f5a4e65674337774670755154376e75725a302f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2219)

*   感谢 [@ben](https://dev.to/ben) 更新了我们的 Airbrake(我们使用的错误监控服务)来忽略一些无关的错误，比如`ActiveRecord::RecordNotFound`。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 忽略不必要的刹车通知 #2283](https://github.com/thepracticaldev/dev.to/pull/2283) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-01T20:33:45Z">Apr 01, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2283)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

我们不需要记录每一条没有找到的记录，因为很多记录来自未发表的文章。它们代表了整体请求的一小部分，但却是我们错误跟踪的一大部分。这清理了一些混乱。

还添加了一个过滤器，随机不跟踪所有的 JS 错误。我们可以只跟踪*一些*,仍然可以在总体上获得相当准确的观察。我们可以以后再来。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2283)

*   [@ben](https://dev.to/ben) 解决了交叉发布的帖子因其原始发布日期而被错误排序的问题。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加原创 _ 发布 _at 获取更准确的交叉发布信息 #2076](https://github.com/thepracticaldev/dev.to/pull/2076) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-15T22:30:49Z">Mar 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2076)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

目前，当一篇文章交叉发布时，我们设置了`published_at`,但是就其在提要中的位置而言，逻辑有点混乱。这不是全部的修复，但这是一个新的专栏，可以更准确地跟踪原始日期，并手动修复几个错误的日期。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2076)

*   @perigk 更新了我们的“附加技术”文档，提到我们使用 Git 和 GitHub 进行版本控制和问题跟踪。谢谢， [@perigk](https://dev.to/perigk) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新 addl-tech . MD# 2078](https://github.com/thepracticaldev/dev.to/pull/2078)

[![PeriGK avatar](img/6e43894b24a896954be5cb746d827b80.png)](https://github.com/PeriGK) **[PeriGK](https://github.com/PeriGK)** posted on [<time datetime="2019-03-16T11:14:59Z">Mar 16, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2078)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [X ]文档更新

## 描述

更新 addl-tech，有两个有点学究气的选项。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ x]文档开发到
*   [ ]自述文件
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2078)

*   [@ben](https://dev.to/ben) 修复了我们的原生页面浏览量跟踪可能出现的竞争情况。详情如下:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复可能的竞态条件与页面查看跟踪 #2103](https://github.com/thepracticaldev/dev.to/pull/2103) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-18T16:36:19Z">Mar 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2103)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

当一个页面视图被跟踪时，我们稍后更新那个视图来跟踪用户仍然在这个页面上。

但有时创建第一个页面视图会有问题。这份公关有助于解释这种情况。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2103)

*   感谢 [@bolariinwa](https://dev.to/bolariinwa) 在标签出现的任何地方添加链接。缺失的地方在[仪表盘](https://dev.to/dashboard)。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 为标签添加链接 #2088](https://github.com/thepracticaldev/dev.to/pull/2088) 

[![bolariin avatar](img/a5411278d9eefe6f01d3157b0ad4f809.png)](https://github.com/bolariin) **[bolariin](https://github.com/bolariin)** posted on [<time datetime="2019-03-17T23:52:00Z">Mar 17, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2088)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [X]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

为了保持标签的可点击性，我更新了 _dashboard_article.html.erb，这样仪表板上帖子的标签被包装在一个链接标签中，并链接到它们各自的页面。

## 相关车票&单据

发行号是#1991

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [X]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2088)

*   @lightalloy 修复了一个反应通知中有移除反应的剩余数据的错误。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复一个反应被破坏时的反应通知 json 数据 #2112](https://github.com/thepracticaldev/dev.to/pull/2112) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-19T12:00:08Z">Mar 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2112)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

当销毁的反应记录被传递给`Notification#send_reaction_notification`时，仅保留通知`json_data`中的持久数据

## 相关车票&单据

#2111

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2112)

*   [@keshavbiswa](https://dev.to/keshavbiswa) 启用了两个 Rubocop 警察。谢谢， [@keshavbiswa](https://dev.to/keshavbiswa) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用 rubo Cop/命名 Cop  #2115](https://github.com/thepracticaldev/dev.to/pull/2115) 

[![keshavbiswa avatar](img/9a1bfb3606eb9bc142dda52990f86c14.png)](https://github.com/keshavbiswa) **[keshavbiswa](https://github.com/keshavbiswa)** posted on [<time datetime="2019-03-19T15:04:36Z">Mar 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2115)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

已启用命名/ascii 标识符复制和命名/文件名复制

## 相关车票&单据

#2021

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2115)

*   [@selbekk](https://dev.to/selbekk) 用我最喜欢的 Rails 方法之一:`pluralize`在需要的时候把单词变成复数，修复了一个 bug。谢谢，[@塞尔贝克](https://dev.to/selbekk)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复单数取复数结尾的 bug# 2069](https://github.com/thepracticaldev/dev.to/pull/2069)

[![selbekk avatar](img/b744ef5e2b1270297fbcb78af79ecfa1.png)](https://github.com/selbekk) **[selbekk](https://github.com/selbekk)** posted on [<time datetime="2019-03-15T13:47:59Z">Mar 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2069)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

我添加了两个 ternaries，当且仅当 count 不为 1 时，在“comment”和“reaction”后面添加一个 s。

## 相关车票&单据

#2067

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![relieved](img/e3e15e67dee51ed1b10ad27447c63901.png)](https://camo.githubusercontent.com/16fb96ea9d44a9d89c68a0c9860aeec63028c59f/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f4a4d5637494b6f717a786c72572f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2069)

*   [@edisonywh](https://dev.to/edisonywh) 修复了一个问题，即进入一个不存在的徽章会导致服务器错误，而不是 404 页面。谢谢， [@edisonywh](https://dev.to/edisonywh) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 重定向到 404 如果徽章没有找到 #2066](https://github.com/thepracticaldev/dev.to/pull/2066) 

[![edisonywh avatar](img/e6f68256bdd5af6fcd0365cc665cd135.png)](https://github.com/edisonywh) **[edisonywh](https://github.com/edisonywh)** posted on [<time datetime="2019-03-14T16:57:07Z">Mar 14, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2066)

以前我们在重定向到`:show`之前不检查`@badge`是否存在，这次提交通过重定向到 404 来解决这个问题。

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

## 相关车票&单据

#2051

## 手机&桌面截图/录音(如有 UI 改动)

[![image](img/b06d936b4e9b17beb21e532719a62375.png)](https://user-images.githubusercontent.com/20277437/54376050-27159580-46bd-11e9-8ee9-4f183d533a73.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![squash bugs!](img/367751be3fa2e74456e695350ce581b9.png)](https://camo.githubusercontent.com/44e898c2e9d8ae98b9a93e5ba60f944f444fb12f/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6f5355746d7268527a357465302f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2066)

*   [@rhymes](https://dev.to/rhymes) 修复了一个长期存在的问题，即一个前端事件`description`没有正确逃脱。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复 MarkdownFixer# 1989](https://github.com/thepracticaldev/dev.to/pull/1989)中无法避免的前面事项描述

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-06T12:41:05Z">Mar 06, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/1989)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

我着手修复[https://github.com/thepracticaldev/dev.to/issues/96](https://github.com/thepracticaldev/dev.to/issues/96)——最初的想法是创建更有意义的错误消息，然后我意识到前面的问题是由第三方库解析的，所以我放弃了这个想法。

我决定只修复由描述中的未包装字符造成的错误(这首先引发了这个问题)

## 相关车票&单据

关闭#96

## 手机&桌面截图/录音(如有 UI 改动)

**在**之前

[![before](img/7ecd8082515734f61317ec299c69b3d4.png)](https://user-images.githubusercontent.com/146201/53881915-2e7dd480-4015-11e9-942c-5d7772a6c2bf.gif)

之后

 **[![after](img/e79a03042802334c17cf4918f74566e5.png)](https://user-images.githubusercontent.com/146201/53881954-49504900-4015-11e9-847b-b388a92bf997.gif)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档** **[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/1989)** ***   [@韵脚](https://dev.to/rhymes)更新了我们的错误引发，以正确反映正确的错误`ActiveRecord::NotFound`而不是`ActionController::RoutingError`。再次感谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 找不到变更 404 应用程序中出现异常控制器 #2118](https://github.com/thepracticaldev/dev.to/pull/2118) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-19T21:49:56Z">Mar 19, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2118)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

在我们进行了大约#2066 的讨论和这个由 [@lightalloy](https://dev.to/lightalloy) 的[评论](https://github.com/thepracticaldev/dev.to/pull/2066/files#r266760867)之后，我提交这个 PR 来改变由`not_found`方法引起的默认异常。

参考消息 Rails 使用`ActionController::RoutingError`和`ActiveRecord::ActionNotFound`呈现 404 页面，所以功能上没有变化。我甚至不确定引发的异常是否使代码更加清晰，因为它对模式是透明的:

```
Model.find_by_(id) || not_found
```

Enter fullscreen mode Exit fullscreen mode

它只是与发现者用`!`加注的方法一致。

上述模式在代码中出现了 15 次，而不是 Rails 默认的:

```
Model.find_by!(id)
```

Enter fullscreen mode Exit fullscreen mode

仅出现 3 次，都在`tags_controller.rb`文件中。

## 相关车票&单据

#2066

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2118)

*   @lightalloy 一直在重构并将通知转移到单独的作业中。此 PR 移动了新的关注者通知。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移动发送新的追随者通知到一个单独的工作 #2030](https://github.com/thepracticaldev/dev.to/pull/2030) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-11T12:49:29Z">Mar 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2030)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   创建了一个单独的活动工单来处理新的关注者通知
*   将属性而不是`Follow`实例传递给作业，以消除反序列化错误。(在这种情况下，我们不能只通过`follow_id`，因为在那一刻`follow`可能会被摧毁)
*   将新的关注者通知逻辑移动到服务对象
*   保留了旧的`Notification`界面(`send_new_follower_notification`和`send_new_follower_notification_without_delay`)

## 相关车票&单据

#1996

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2030)

*   [@keshavbiswa](https://dev.to/keshavbiswa) 启用了`Rubocop/Rails`警察。谢谢， [@keshavbiswa](https://dev.to/keshavbiswa) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用 Rubocop/Rails cops# 2125](https://github.com/thepracticaldev/dev.to/pull/2125)

[![keshavbiswa avatar](img/9a1bfb3606eb9bc142dda52990f86c14.png)](https://github.com/keshavbiswa) **[keshavbiswa](https://github.com/keshavbiswa)** posted on [<time datetime="2019-03-20T15:43:39Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2125)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

已启用 Rubocop/Rails cops

## 相关车票&单据

#2021

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2125)

*   @glennmen 重构了我们授予年度徽章的方式，这种方式比我们之前的实施方式*灵活得多*。谢谢， [@glennmen](https://dev.to/glennmen) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 重构奖项 _ 年度 _ 俱乐部 _ 徽章 #2035](https://github.com/thepracticaldev/dev.to/pull/2035) 

[![Glennmen avatar](img/b208733bf7be977cfd1b09cfcfd1f625.png)](https://github.com/Glennmen) **[Glennmen](https://github.com/Glennmen)** posted on [<time datetime="2019-03-11T20:26:24Z">Mar 11, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2035)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

重构`award_yearly_club_badges`,这样添加新的年度徽章会更容易，需要的改动也更少。

我有几个关于重构的问题:

*   你想要循环播放还是更像`award_streak_badge(num_weeks)`？我认为 for 循环是一个更好的选择，你只需要增加一个变量，而不需要增加新的一行到`fetch.rake`[https://github . com/thepracticaldev/dev . to/blob/0 b 84 AE 9 acba 3658 af 1112 aeb 520 a 049 e 852 ffd 27/app/labor/badge _ reward . Rb # L47](https://github.com/thepracticaldev/dev.to/blob/0b84ae9acba3658af1112aeb520a049e852ffd27/app/labor/badge_rewarder.rb#L47)[https://github . com/thepracticaldev/dev . to/blob/0 b 844](https://github.com/thepracticaldev/dev.to/blob/0b84ae9acba3658af1112aeb520a049e852ffd27/lib/tasks/fetch.rake#L73-L75)

*   你们认为设置参数的最佳方式是什么？我目前将它作为变量添加到函数中。

*   将 slug 改为数字数值而不是单词值(`one-year-club` - > `1-year-club`)会破坏现有徽章吗？你认为如何最好地处理这件事？

欢迎任何其他反馈！

## 相关车票&单据

[https://dev.to/ben/comment/8inc](https://dev.to/ben/comment/8inc)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![birthday](img/bed22ba5d13fc87166732506a2986372.png)](https://camo.githubusercontent.com/d0af1878ce900d80725ffc7eabf465b24bff6f71/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f796f4a4332476e53436c62504f6b563065412f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2035)

*   [@维纳利斯](https://dev.to/venarius)对 UX 做了一些改进，将`editor_version`字段更新为`<select>`字段。这原本是一个`<text>`领域。谢谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UX:将 editor_version 改为选择字段 #2107](https://github.com/thepracticaldev/dev.to/pull/2107) 

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-03-18T21:31:34Z">Mar 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2107)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

将`editor_version`输入`settings/misc`更改为`select`字段

## 相关车票&单据

问题#1775

## 手机&桌面截图/录音(如有 UI 改动)

[![Screenshot from 2019-03-18 22-29-23](img/8c82772f0ff94a6077ffab3598bf8766.png)](https://user-images.githubusercontent.com/13546486/54564917-4f5b0800-49cd-11e9-9c9c-02d97602a795.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [ x]不需要文档

**这也是我第一次公关发展到** <g-emoji class="g-emoji" alias="partying_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f973.png">🥳</g-emoji>

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2107)

*   [@维纳利斯](https://dev.to/venarius)也启用了`Rubocop/lint`警察。再次感谢，[@维纳利斯](https://dev.to/venarius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用 rubo COP/lint# 2130](https://github.com/thepracticaldev/dev.to/pull/2130)

[![venarius avatar](img/2ff372d78ec1fb26175104789e54c0b5.png)](https://github.com/venarius) **[venarius](https://github.com/venarius)** posted on [<time datetime="2019-03-20T18:27:04Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2130)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这启用了 rubocop/lint

## 相关车票&单据

[https://github.com/thepracticaldev/dev.to/issues/2021](https://github.com/thepracticaldev/dev.to/issues/2021)

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [ x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2130)

*   [@abraham](https://dev.to/abraham) 更新了 Faker 宝石，修复了一个不赞成使用的 Faker::Overwatch 用法。谢谢， [@abraham](https://dev.to/abraham) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新假货并修复弃用的假货::看守使用 #2142](https://github.com/thepracticaldev/dev.to/pull/2142) 

[![abraham avatar](img/429531d9f567abaef8022a71b195b789.png)](https://github.com/abraham) **[abraham](https://github.com/abraham)** posted on [<time datetime="2019-03-21T03:28:47Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2142)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

更新`Faker::Overwatch.quote`并修复测试中的弃用通知。

> 注意:Faker::Overwatch.quote 已被弃用；请改用 Faker::Games::Overwatch.quote。它将于 2019 年 1 月 1 日或之后被删除。Faker::Overwatch.quote 调用自/home/Travis/build/Abraham/dev . to/spec/factories/baggets . Rb:7。

## 相关车票&单据

不适用的

## 手机&桌面截图/录音(如有 UI 改动)

不适用的

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2142)

*   [@押韵](https://dev.to/rhymes)增加了组织 logo 的替代文字。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 为组织标志添加替代文字 #2148](https://github.com/thepracticaldev/dev.to/pull/2148) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-21T10:05:14Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2148)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

组织徽标在一些地方缺少替代文本。

感谢灯塔在主页上捕捉到这个。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

[![Screenshot 2019-03-21 at 11 04 29 AM](img/b200970d68f452c86cdd202ef2260383.png)](https://user-images.githubusercontent.com/146201/54745152-299a5280-4bc9-11e9-9866-b9acef3176c0.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2148)

*   [@韵](https://dev.to/rhymes)用适当的伐木代替了所有的红宝石`put`。谢谢你。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 用合适的测井代替放入 #2139](https://github.com/thepracticaldev/dev.to/pull/2139) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-20T23:05:03Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2139)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

在检查另一个 PR 时，我注意到在一个错误处理程序中有一个`puts`,我决定使用适当的日志记录，最终替换了所有的`puts`。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2139)

*   [@link2twenty](https://dev.to/link2twenty) 修复了侧边栏的 CSS 问题。谢谢， [@link2twenty](https://dev.to/link2twenty) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复工具条-个人资料-快照宽度 #2145](https://github.com/thepracticaldev/dev.to/pull/2145) 

[![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2019-03-21T09:26:54Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2145)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

程序员面临的 3 个最困难的问题是

1.  命名事物
2.  相差 1 个误差

`.sidebar-profile-snapshot`宽度比工具条的其余部分宽 1px。

## 手机&桌面截图/录音(如有 UI 改动)

之前: [![image](img/a787bcc8099683e9cec599c9248768fa.png)](https://user-images.githubusercontent.com/3534427/54743006-d9b48f00-4bba-11e9-9679-b239efa0bd97.png)

[![image](img/b3932dc3783f0bac81165c76c2c4329e.png)](https://user-images.githubusercontent.com/3534427/54743081-16808600-4bbb-11e9-9883-adbbfb9fc30c.png) 之后

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2145)

*   [@abraham](https://dev.to/abraham) 切换了一个 gem 源码使用了发布版而不是 fork。谢谢， [@abraham](https://dev.to/abraham) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 切换到发布版本 #2161](https://github.com/thepracticaldev/dev.to/pull/2161) 

[![abraham avatar](img/429531d9f567abaef8022a71b195b789.png)](https://github.com/abraham) **[abraham](https://github.com/abraham)** posted on [<time datetime="2019-03-21T18:16:58Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2161)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

从 fork 切换到发布版本。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2161)

*   [@link2twenty](https://dev.to/link2twenty) 针对夜间模式做了几处改动。谢谢， [@link2twenty](https://dev.to/link2twenty) ！

    *   [移除夜间模式下评论框的白色边框](https://github.com/thepracticaldev/dev.to/pull/2149)
    *   [移除侧边栏的白色边框](https://github.com/thepracticaldev/dev.to/pull/2146)
    *   [更新阅读列表侧边栏颜色](https://github.com/thepracticaldev/dev.to/pull/2151)
    *   [添加了组织链接](https://github.com/thepracticaldev/dev.to/pull/2168)的可识别文本
*   [@cyrillefr](https://dev.to/cyrillefr) 启用了性能相关的 Rubocop。谢谢， [@cyrillefr](https://dev.to/cyrillefr) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用性能 rubocop cops# 2140](https://github.com/thepracticaldev/dev.to/pull/2140)

[![cyrillefr avatar](img/7ef3538029145d4cf304bfb0f0cca6f5.png)](https://github.com/cyrillefr) **[cyrillefr](https://github.com/cyrillefr)** posted on [<time datetime="2019-03-20T23:25:32Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2140)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

已启用所有性能警察。在需要的地方重构。

## 相关车票&单据

[https://github.com/thepracticaldev/dev.to/issues/2021](https://github.com/thepracticaldev/dev.to/issues/2021)

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![alt_text](img/833353087bc2d74e86cce076c05c4a4e.png)](gif_link)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2140)

*   [@韵](https://dev.to/rhymes)去掉了一些死代码！谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除 klipse 脚本未使用的部分 #2164](https://github.com/thepracticaldev/dev.to/pull/2164) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-21T20:19:00Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2164)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

在处理#2153 时，我注意到这个部分没有在任何地方使用

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2164)

*   @rhymes 做了一个安全改进，并在脚本标签中添加了 [SRI(子资源完整性)](https://infosec.mozilla.org/guidelines/web_security#subresource-integrity)。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在适用的地方给脚本标签添加 SRI# 2153](https://github.com/thepracticaldev/dev.to/pull/2153)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-21T11:29:41Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2153)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

出于安全考虑，所有外部脚本都应该启用 [SRI](https://infosec.mozilla.org/guidelines/web_security#subresource-integrity) (又名子资源完整性)。这允许浏览器检查外部源是否已经被篡改(从而不加载脚本)。

我只添加了那些公开信息并指向特定版本资源的网站。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2153)

*   @mscccc 通过使用 HTML/CSS 对图片进行渲染，改进了我们的社交卡片。详情请看公关。谢谢， [@mscccc](https://dev.to/mscccc) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 社会卡升级-第一部分 #2090](https://github.com/thepracticaldev/dev.to/pull/2090) 

[![mscoutermarsh avatar](img/aa8dbaf941eee25731d4eeb667e775cd.png)](https://github.com/mscoutermarsh) **[mscoutermarsh](https://github.com/mscoutermarsh)** posted on [<time datetime="2019-03-18T01:19:39Z">Mar 18, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2090)

# [开发到](https://dev.to) + [HTML/CSS 到图像](https://htmlcsstoimage.com) <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡️</g-emoji>

<g-emoji class="g-emoji" alias="wave" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44b.png">👋</g-emoji> @benhalpern

这赋予了`SocialPreviewsController`渲染`png`的能力。

**当前网址:** `https://res.cloudinary.com/practicaldev/image/url2png/s--pncSBGoq--/c_fill,g_north,h_400,w_800/https://dev.to/social_previews/article/75561%3Fbust%3D0-Build%20a%20GitHub%20Action%20with%20Ruby-true"`

**未来:** `https://dev.to/social_previews/article/89948.png`

## 花式 Gif

[![Screen Recording 2019-03-16 at 03 07 PM](img/41a59cb370762a3e089abd85e0428350.png)](https://user-images.githubusercontent.com/155044/54500709-ef853400-48dc-11e9-9875-4755562883c6.gif)

## 向后兼容

实现这一点，以便此 PR 是一个无风险的部署。什么都没坏。所有旧的网址都和以前完全一样。social_preview 路线现在也支持`.png`。

## 缓存是如何工作的？

呈现的 html 被用作缓存键(Dalli SHAs it)。这意味着，我们可以“免费”破坏缓存，每当模板改变，或者模板中包含的数据(如文章标题或用户名)更新时，缓存都会破坏。

为此，我必须去除模板中的一些随机性。它们现在是“确定随机的”，意味着对象的 ID 被用作种子。所以每次你都会得到相同的随机数序列。

## 发布计划

*   [ ]设置`HCTI_API_USER_ID`和`HCTI_API_KEY`环境变量。
*   [ ]发货
*   [ ]检查一些预览网址，确保它们看起来 <g-emoji class="g-emoji" alias="sparkles" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2728.png">✨</g-emoji>
*   [ ]跟进 PR 以更新元标签，指向新的 URL

## 这是什么类型的公关？(勾选所有适用选项)

*   [X]功能

## 手机&桌面截图/录音(如有 UI 改动)

[![image](img/c3e7d17768bd2d362acabb80bd67cc9e.png)](https://user-images.githubusercontent.com/155044/54500791-b8635280-48dd-11e9-8739-36e10b2f5b2a.png)([https://hcti . io/v1/image/e 632881d-b0f 6-4d 33-be3f-a 5863 cf 260 a 2](https://hcti.io/v1/image/e632881d-b0f6-4d33-be3f-a5863cf260a2))

[![image](img/9723c5d542799bc3710f4026dd5f3e4a.png)](https://user-images.githubusercontent.com/155044/54500915-12184c80-48df-11e9-8eef-5114e0571bbd.png)([https://hcti . io/v1/image/7455 df4e-636 e-4 bed-b0f 6-90cb 419977 FB](https://hcti.io/v1/image/7455df4e-636e-4bed-b0f6-90cb419977fb))

[![image](img/b120e723f25732ee8fb03c9d9fac4254.png)](https://user-images.githubusercontent.com/155044/54501005-a7b3dc00-48df-11e9-84f1-7ccf86859727.png)([https://hcti . io/v1/image/6 c 52 de 9d-4d 37-4008-80 F8-67155589 E1 a 1](https://hcti.io/v1/image/6c52de9d-4d37-4008-80f8-67155589e1a1))

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2090)

*   [@brylie](https://dev.to/brylie) 将乳齿象实例`social.coop`添加到我们的允许列表中。谢谢， [@brylie](https://dev.to/brylie) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加 social . coop# 2170](https://github.com/thepracticaldev/dev.to/pull/2170)

[![brylie avatar](img/ed6f8a4572f3182f944fbcbb8dcda2ff.png)](https://github.com/brylie) **[brylie](https://github.com/brylie)** posted on [<time datetime="2019-03-22T09:37:04Z">Mar 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2170)

关闭#2169

将[https://social . coop](https://social.coop)mast adon 实例添加到可信列表。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2170)

*   [@glennmen](https://dev.to/glennmen) 为里程碑通知的 gif 添加了一个缺失的`/`。谢谢， [@glennmen](https://dev.to/glennmen) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)【bug fix broken giphy URL】【2180】](https://github.com/thepracticaldev/dev.to/pull/2180)

[![Glennmen avatar](img/b208733bf7be977cfd1b09cfcfd1f625.png)](https://github.com/Glennmen) **[Glennmen](https://github.com/Glennmen)** posted on [<time datetime="2019-03-22T21:59:03Z">Mar 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2180)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

修复不正确的 giphy url，以便 gif 可以正确加载。

## 相关车票&单据

修复#2179

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2180)

*   [@abraham](https://dev.to/abraham) 启用了许多`Rubocop/Rails`警察，并解决了一些问题。谢谢，[@亚伯拉罕](https://dev.to/abraham)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用 Rails cops  #2186](https://github.com/thepracticaldev/dev.to/pull/2186) 

[![abraham avatar](img/429531d9f567abaef8022a71b195b789.png)](https://github.com/abraham) **[abraham](https://github.com/abraham)** posted on [<time datetime="2019-03-24T18:36:25Z">Mar 24, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2186)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

默认情况下，Ruby on Rails Rubocop 规则是禁用的。这使得他们中的大多数。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![dumbo taking a bath](img/4dce7448a4137bb9ef3b7486b8c82451.png)](https://camo.githubusercontent.com/b49b504343895437e754a50e4a166d0f46722f4e/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f534a333667303631374e6836302f67697068792d646f776e73697a65642e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2186)

*   @abraham 稍微简化了我们的开发设置。详情请看公关。再次感谢， [@abraham](https://dev.to/abraham) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 简化开发设置 #2143](https://github.com/thepracticaldev/dev.to/pull/2143) 

[![abraham avatar](img/429531d9f567abaef8022a71b195b789.png)](https://github.com/abraham) **[abraham](https://github.com/abraham)** posted on [<time datetime="2019-03-21T04:42:41Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2143)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

通过使用默认数据库配置的惯例，删除`bin/setup`处理的手动步骤，并将`foremen`添加到开发 gems 组，减少了设置开发的步骤(11 步 mac/linux 和 13 步 windows 到 7 步 mac/linux)。

## 相关车票&单据

[https://github.com/thepracticaldev/dev.to/pull/1162](https://github.com/thepracticaldev/dev.to/pull/1162)

## 手机&桌面截图/录音(如有 UI 改动)

不适用的

## 添加到文档中？

*   [x]文档开发到
*   [x] readme
*   [ ]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![working out](img/73ccb43f51d45e8bcbdf0f5147a20124.png)](https://camo.githubusercontent.com/d35bcb6f8e44863de444a97acdb3f1aab9b089b1/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f336d4a79664446483042716762646768574a2f67697068792d646f776e73697a65642e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2143)

*   [@lightalloy](https://dev.to/lightalloy) 将新的反应通知方法移动到一个单独的作业中。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将新的反应通知方法移动到单独的 ActiveJob  #2122](https://github.com/thepracticaldev/dev.to/pull/2122) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-20T13:26:18Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2122)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [x]错误修复

## 描述

*   创建了一个单独的活动工单来处理新的反应通知
*   将属性而不是反应和接收器实例传递给作业，以避免反序列化错误
*   将新的反应通知逻辑移动到服务对象
*   保留了旧的`Notification`界面(方法`send_resend_reaction_notification`、`send_reaction_notification_without_delay`)

## 相关车票&单据

#1996 #1621

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2122)

*   [@dabrorius](https://dev.to/dabrorius) 移除了未使用的`/dashboard/following_users`路线。谢谢，[@达布罗里斯](https://dev.to/dabrorius)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 删除`仪表板/跟随 _ 用户`路线 #2191](https://github.com/thepracticaldev/dev.to/pull/2191) 

[![dabrorius avatar](img/08546437ad0d94cda8506678b76460b1.png)](https://github.com/dabrorius) **[dabrorius](https://github.com/dabrorius)** posted on [<time datetime="2019-03-25T10:18:37Z">Mar 25, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2191)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

`dashboard/following_users`路由前阵子已经被弃用，取而代之的是`dashboard/following`路由。该提交删除旧路由，并更新代码以反映它。

## 相关车票&单据

这个在这里和@benhalpern 简单讨论了一下:[https://github . com/the practical dev/dev . to/pull/2157 # issue comment-475366986](https://github.com/thepracticaldev/dev.to/pull/2157#issuecomment-475366986)

## 手机&桌面截图/录音(如有 UI 改动)

没有用户界面更改

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![wintermoon](img/291082667036d90282072ae10c2fe9a7.png)](https://camo.githubusercontent.com/69c14d4d6c0d1576b374a8c9b0480f4ca840ba97/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f715932784554364b64594139692f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2191)

*   [@jess](https://dev.to/jess) 更新了自述。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 更新自述 #2206](https://github.com/thepracticaldev/dev.to/pull/2206) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-03-25T19:24:06Z">Mar 25, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2206)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [x]文档更新

## 描述

*   删除产品路线图，因为我们从来没有这个习惯
*   要求前端变化的设计模型
*   将 mario 和 anna 更新到贡献者列表

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2206)

*   [@aspittel](https://dev.to/aspittel) 修复了 pro 仪表盘的一些夜间模式问题。谢谢你，阿里！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 特色/专业仪表盘 #2178](https://github.com/thepracticaldev/dev.to/pull/2178) 

[![aspittel avatar](img/f12052c0aff846de845fda5d2ebdf5f0.png)](https://github.com/aspittel) **[aspittel](https://github.com/aspittel)** posted on [<time datetime="2019-03-22T20:29:12Z">Mar 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2178)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [X]功能
*   [X]错误修复
*   [ ]文档更新

## 描述

修复了专业仪表板的黑暗主题问题(虽然还可以进一步清理)，也删除了底部最近的反应器。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2178)

*   @lightalloy 干涸了一些活跃的工作测试。安娜，谢谢你！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 干现役职务规格 #2215](https://github.com/thepracticaldev/dev.to/pull/2215) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-26T12:38:36Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2215)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

创建了一个共享示例来测试调用`#perform_later`时活动作业是否排队

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2215)

*   [@ben](https://dev.to/ben) 清理了一些新的聊天频道功能，增加了测试。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 清理新聊天频道功能并添加测试 #2220](https://github.com/thepracticaldev/dev.to/pull/2220) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-26T16:35:14Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2220)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

重构并添加对聊天频道功能的测试。保持了大致相同的代码排列。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2220)

*   @lito 通过使用原生 Rails 实现改进了我们的 slug 生成。谢谢， [@lito](https://dev.to/lito) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 改良版鼻涕虫一代 #2218](https://github.com/thepracticaldev/dev.to/pull/2218) 

[![eusonlito avatar](img/7a54ef3a3748d2fde55804cac587546a.png)](https://github.com/eusonlito) **[eusonlito](https://github.com/eusonlito)** posted on [<time datetime="2019-03-26T15:30:19Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2218)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [X]功能
*   [X]错误修复
*   [ ]文档更新

## 描述

Ruby on Rails 自 Rails 2.2[https://www . ruby doc . info/github/Rails/Rails/String:parameterize](https://www.rubydoc.info/github/rails/rails/String:parameterize)起就有内置的 slug 生成器

这个生成器修复了标题中使用非英文字符时产生的问题，提高了搜索引擎优化。例如:[https://dev . to/lito _ ordes/redimensionando-im genes-en-tiempo-de-ejecucin-con-packer-57c 6](https://dev.to/lito_ordes/redimensionando-imgenes-en-tiempo-de-ejecucin-con-packer-57c6)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2218)

*   @lightalloy 启用了几个没有违规的 Rubocop 警察。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 启用了几个没有违规的 rubocop 警察 #2214](https://github.com/thepracticaldev/dev.to/pull/2214) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-26T10:52:21Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2214)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

启用了几个没有违规的机械警察

## 相关车票&单据

#2021

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2214)

*   [@dabrorius](https://dev.to/dabrorius) 通过从`DashboardsController`中提取`following`动作来重构`show`动作。谢谢， [@dabrorius](https://dev.to/dabrorius) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 从仪表盘的“显示”中提取“跟随”动作控制器 #2212](https://github.com/thepracticaldev/dev.to/pull/2212) 

[![dabrorius avatar](img/08546437ad0d94cda8506678b76460b1.png)](https://github.com/dabrorius) **[dabrorius](https://github.com/dabrorius)** posted on [<time datetime="2019-03-26T06:39:32Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2212)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

## 相关车票&单据

目前在 DashboardsController 的`show`作用下，几个独立的页面都被塞满了。这是尝试重构该动作并将其分成几个更小的控制器动作的第一步。

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![blek](img/fd9cff739c612c648275161de986f9a6.png)](https://camo.githubusercontent.com/44c277cdbab121f07636a6831c42130e40f59653/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f7a716b3339527739323032584b2f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2212)

*   [@lightalloy](https://dev.to/lightalloy) 修复了几个 Rubocop 的问题。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)【ruboop fixed】【2232】](https://github.com/thepracticaldev/dev.to/pull/2232)

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-28T09:50:47Z">Mar 28, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2232)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   修正了几个机械战警的问题
*   从`.rubocop.yml`中删除了固定的和与默认相同的零件
*   将需要修复的残疾警察从`.rubocop.yml`移至`.rubocop_todo.yml`

## 相关车票&单据

#2021

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2232)

*   [@jess](https://dev.to/jess) 解决了合并用户时清除旧缓存的问题。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 合并用户时清除缓存 #2229](https://github.com/thepracticaldev/dev.to/pull/2229) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-03-28T01:28:38Z">Mar 28, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2229)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

*   更新计数器缓存
*   保留旧的创建日期
*   快速缓存崩溃

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2229)

*   [@lightalloy](https://dev.to/lightalloy) 开始通过增加一些唯一性指数来优化签到流程。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 优化签到:身份#2061  #2231](https://github.com/thepracticaldev/dev.to/pull/2231) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-28T08:46:33Z">Mar 28, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2231)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   在`Identity`添加了缺失的唯一索引
*   删除了冗余的唯一验证(`provider` + `uid` vs `uid` + `provider`)
*   使唯一性验证有条件:仅在范围的一个字段被更改时检查唯一性。这是一个针对标签#2061 的微小优化

## 合并前要考虑的事情:

*   检查现有的无效`Identity`记录，它们需要被修复
*   表可能很大，所以添加索引可能需要很长时间

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2231)

*   [@jess](https://dev.to/jess) 在标签权重表单中添加了 HTML 表单验证，以防止`nil`值。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加表单验证为跟随# 2236](https://github.com/thepracticaldev/dev.to/pull/2236)

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-03-28T21:24:00Z">Mar 28, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2236)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x ]错误修复
*   [ ]文档更新

## 描述

*   如果用户在仪表板上提交一个零/空白字段，下次他们试图访问时，它会给他们一个 500。
*   这增加了一个表单验证，但在服务器端没有验证。我们可能也应该这样做，但当我在 Follow 模型上添加 presence: true 时，它做了一些奇怪的事情，所以我认为快速修复更有意义。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2236)

*   [@lightalloy](https://dev.to/lightalloy) 解决了导致一些新评论通知无法发送的逻辑错误。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复通知关于新孩子的评论 #2233](https://github.com/thepracticaldev/dev.to/pull/2233) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-28T12:24:24Z">Mar 28, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2233)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]错误修复

## 描述

发布子评论时，只有在没有其他用户需要通知此评论时，文章(可评论)作者才会收到通知。我认为向文章作者发送通知不应该取决于它是否发送给了其他用户。如果`receive_notifications`为真，那么应该通知文章作者关于子评论或者不通知他们。我添加了几个测试来说明这个问题，并假设应该通知文章作者关于子评论的问题。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2233)

*   [@peiche](https://dev.to/peiche) 修复了一个组织在夜间模式下的总结文字颜色。谢谢，[@陪车](https://dev.to/peiche)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 固定粘性导航摘要颜色使用自定义属性。 #2240](https://github.com/thepracticaldev/dev.to/pull/2240) 

[![peiche avatar](img/2b452615f9e47f7d0ccc776bdd9cf344.png)](https://github.com/peiche) **[peiche](https://github.com/peiche)** posted on [<time datetime="2019-03-29T19:31:13Z">Mar 29, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2240)

## 这是什么类型的公关？

*   [ ]重构
*   [ ]功能
*   [X]错误修复
*   [ ]文档更新

## 描述

即使在黑暗模式下，粘性导航摘要文本也是黑色的。此修复程序允许它使用自定义属性，以黑色文本颜色作为后备。

## 截图

### 以前

[![Screenshot of before change](img/d47658269b092eeff7a52e32c6f22123.png)](https://user-images.githubusercontent.com/613931/55257390-bb3f3b00-522d-11e9-92d4-bddc25c2b708.png)

### 后

[![Screenshot of after change](img/82e5fd050d1f1d3ae1884e7703544416.png)](https://user-images.githubusercontent.com/613931/55257407-cbefb100-522d-11e9-82db-30cd4e60ebd1.png)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [X]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2240)

*   [@maestromac](https://dev.to/maestromac) 移除了控制器测试，支持请求测试。谢谢你，麦克！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移除控制器规格 #2224](https://github.com/thepracticaldev/dev.to/pull/2224) 

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2019-03-27T16:23:53Z">Mar 27, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2224)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   将控制器规格移动到请求规格。
*   移除导轨-控制器-测试 gem。
*   重新组织文章请求规范。
*   添加附加规格。

## 相关车票&单据

第 1827 号决议

## 手机&桌面截图/录音(如有 UI 改动)

不适用的

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2224)

*   [@perlatsp](https://dev.to/perlatsp) 修复了`/settings/integrations`中 GitHub repos 的文本。谢谢， [@perlatsp](https://dev.to/perlatsp) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复 Github 库不可见#2227  #2241](https://github.com/thepracticaldev/dev.to/pull/2241) 

[![perlatsp avatar](img/b7d7ddbe805ae27121ea7bc77b9c85be.png)](https://github.com/perlatsp) **[perlatsp](https://github.com/perlatsp)** posted on [<time datetime="2019-03-29T23:10:31Z">Mar 29, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2241)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

在设置/集成页面中为 GitHub 存储库容器添加主题容器背景色。侧边栏容器使用的背景颜色变量。

```
--theme-container-background 
```

Enter fullscreen mode Exit fullscreen mode

## 相关车票&单据

#2227

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2241)

*   修复了 articles API 的一个错误，在这个错误中，使用未知用户名发送请求会返回 500 错误。PR 更新后返回一个空数组。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复文章 API 错误和未知用户名 #2247](https://github.com/thepracticaldev/dev.to/pull/2247) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-31T12:28:39Z">Mar 31, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2247)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

目前，如果使用未知的用户名调用服务器，articles API 会出现 500 错误，因为`not_found`是一个控制器方法，不是服务的一部分。

我相信它不应该像用未知标签调用 API 那样返回任何东西。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2247)

*   [@押韵](https://dev.to/rhymes)对文章 API 端点进行了一点重构，只显示已发布的文章。再次感谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在 SQL 查询中添加发表检索文章 #2248](https://github.com/thepracticaldev/dev.to/pull/2248) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-31T15:21:15Z">Mar 31, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2248)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

因为我们已经请求数据库检索一篇文章，所以我们可以直接请求它过滤掉已发表的文章，而无需在运行时进行检查。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2248)

*   [@dabrorius](https://dev.to/dabrorius) 通过从`DashboardsController`中提取`followers`动作来重构`show`动作。谢谢， [@dabrorius](https://dev.to/dabrorius) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 从仪表盘的“显示”中提取“关注者”动作控制器 #2270](https://github.com/thepracticaldev/dev.to/pull/2270) 

[![dabrorius avatar](img/08546437ad0d94cda8506678b76460b1.png)](https://github.com/dabrorius) **[dabrorius](https://github.com/dabrorius)** posted on [<time datetime="2019-04-01T14:42:18Z">Apr 01, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2270)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这是重构 DashboardsController 的第二步，其中`followers`动作与通用的`show`动作分离。我现在保持所有路线名称不变，我们可能会在后续的 PR 中更新它们。

## 相关车票&单据

这是这部电影的后续公关:[https://github.com/thepracticaldev/dev.to/pull/2212](https://github.com/thepracticaldev/dev.to/pull/2212)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![immortal](img/5fa35b4d2ce41d15941fe95bb3797eda.png)](https://camo.githubusercontent.com/ef0e41a81e423383232474466bacd3fd5b7b7214/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f7034787034426a484964616e652f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2270)

*   [@mjraadi](https://dev.to/mjraadi) 修复了帖子评论部分顶部的帖子预览片段的错误着色。谢谢， [@mjraadi](https://dev.to/mjraadi) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复夜间模式问题#2244  #2285](https://github.com/thepracticaldev/dev.to/pull/2285) 

[![mjraadi avatar](img/4244e10cdc945dace6882cda739d9b26.png)](https://github.com/mjraadi) **[mjraadi](https://github.com/mjraadi)** posted on [<time datetime="2019-04-02T07:16:18Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2285)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新
*   [x]补丁夜间模式样式

## 手机&桌面截图/录音(如有 UI 改动)

以下是样式修正:

[![Screenshot_20190402_114000](img/fcc204c3fe9c729914a97fc90927a242.png)](https://user-images.githubusercontent.com/20579660/55382995-48d79f00-553c-11e9-88a7-33d55c9fcda6.png)

关闭:#2244

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2285)

*   [@lightalloy](https://dev.to/lightalloy) 为用户邮件增加了唯一性索引。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 优化签到:用户 #2237](https://github.com/thepracticaldev/dev.to/pull/2237) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-29T12:12:37Z">Mar 29, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2237)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   给`users`邮件添加了一个独特的索引
*   仅当相应字段发生更改时，才验证用户电子邮件和用户名

需要检查没有重复的电子邮件或用户名的用户。

我还想将`twitter_username`和`github_username`验证改为有条件的，但这需要将`allow_blank`改为`allow_nil`无效，将空值更新为 nils，并确保不会有任何向数据库写入空值的尝试。

## 相关车票&单据

#2061

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2237)

*   @ben 做了一个优化，只在用户的 RSS 提要 URL 改变时才验证它。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 有条件运行饲料验证 #2292](https://github.com/thepracticaldev/dev.to/pull/2292) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-04-02T21:11:05Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2292)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

在当前的设置下，检查 RSS 提要的有效性是非常昂贵的。只有当事情发生变化时，我们才应该这样做。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2292)

*   [@lightalloy](https://dev.to/lightalloy) 为 Twitter 和 GitHub 用户名增加了唯一性指数。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 独特的 twitter 和 github 用户名#2061  #2255](https://github.com/thepracticaldev/dev.to/pull/2255) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-01T10:25:47Z">Apr 01, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2255)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

我制作此 pr 是为了准备将`User` `github` / `twitter_username`的唯一验证更改为有条件的。这将有助于#2061

*   将 github 和 twitter 用户名设置为`nil`，以防它们是空字符串:在`User`模型和`Admin::UsersController`中(以防万一)
*   为数据库中的这些字段添加了唯一索引

合并前:

*   确保`users`表中没有重复`twitter_username`或`github_username`的记录
*   确保这些字段中没有空字符串值的记录(在这种情况下，将它们更新为 nil)。

## 相关车票&单据

#2061

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2255)

*   [@押韵](https://dev.to/rhymes)为`PodcastEpisodesController`增加了测试。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加测试到播客视频控制器 #2297](https://github.com/thepracticaldev/dev.to/pull/2297) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-03T11:37:05Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2297)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这个公关做两件事:

*   为 Api::v 0::PodcastEpisodesController 添加测试
*   删除未使用的路线和代码

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2297)

*   [@mjraadi](https://dev.to/mjraadi) 修复了夜间模式下“上传视频”部分的背景色。谢谢， [@mjraadi](https://dev.to/mjraadi) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复了夜间模式下上传视频片段的背景颜色主题#2245  #2296](https://github.com/thepracticaldev/dev.to/pull/2296) 

[![mjraadi avatar](img/4244e10cdc945dace6882cda739d9b26.png)](https://github.com/mjraadi) **[mjraadi](https://github.com/mjraadi)** posted on [<time datetime="2019-04-03T05:44:21Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2296)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新
*   [x]补丁夜间模式样式

## 手机&桌面截图/录音(如有 UI 改动)

以下是造型修正: [![Screenshot_20190402_121830](img/50ac75204fc9075d0949a80913bbc615.png)](https://user-images.githubusercontent.com/20579660/55455524-31122080-55f9-11e9-8735-8085cd992327.png)

关闭:#2245

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2296)

*   [@lightalloy](https://dev.to/lightalloy) 将新的评论通知移动到一个单独的作业。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 新评论通知到活动工单#1996  #2287](https://github.com/thepracticaldev/dev.to/pull/2287) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-04-02T10:50:39Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2287)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [x]错误修复

## 描述

*   将发送有关新注释的通知移动到单独的活动工单
*   ActiveJob 接收`comment_id`而不是`Comment`实例。这修复了试图发送关于被破坏的注释的通知的可能问题。
*   向服务对象发送有关新注释逻辑的通知
*   评论通知功能的规格

## 相关车票&单据

#1996 #1621

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2287)

*   [@m1guelpf](https://dev.to/m1guelpf) 修复了 Blogcast 液体标签的一些样式问题。谢谢， [@m1guelpf](https://dev.to/m1guelpf) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 修复博客造型问题 #2298](https://github.com/thepracticaldev/dev.to/pull/2298) 

[![m1guelpf avatar](img/304a99a72fc3d5f4d10d4e1381d8977b.png)](https://github.com/m1guelpf) **[m1guelpf](https://github.com/m1guelpf)** posted on [<time datetime="2019-04-03T16:13:48Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2298)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [ ]功能
*   [x]错误修复
*   [ ]文档更新

## 描述

我注意到 Blogcast 嵌入在移动设备上显示得不太好，而且还在它周围创建了一些奇怪的间距。这个公关应该可以解决这两个问题。

## 相关车票&单据

## 手机&桌面截图/录音(如有 UI 改动)

之前: [![Image 2019-04-03 at 6 12 31 PM](img/e90dbc37d50a62ac4bcefe75f0b71365.png)](https://user-images.githubusercontent.com/23558090/55507467-03a88f80-5658-11e9-8f97-70417afbab45.png)

[![Image 2019-04-03 at 6 12 53 PM](img/bacc2e46a418c205a775699bd2cb8439.png)](https://user-images.githubusercontent.com/23558090/55507491-10c57e80-5658-11e9-94aa-77e3f4c7aef0.png) 之后

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2298)

*   [@maestromac](https://dev.to/maestromac) 将我们所有的特性测试迁移到系统测试。谢谢你，麦克！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将特性测试迁移到系统测试 #2301](https://github.com/thepracticaldev/dev.to/pull/2301) 

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2019-04-03T19:43:23Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2301)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]杂项

## 描述

*   从功能规格切换到系统规格
    *   这使得我们可以删除数据库清洁，也需要降级水豚。
*   修正了一些 rspec 的不一致性。

## 相关车票&单据

不适用的

## 手机&桌面截图/录音(如有 UI 改动)

不适用的

## 添加到文档中？

*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2301)

*   [@押韵](https://dev.to/rhymes)重构了`AnalyticsService`以使用 SQL 查询代替内存调用。这是一个正在进行的重构，关于实现细节的讨论正在进行。如果你感兴趣，请随时插话！再次感谢帮忙，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 简化分析服务代码 #2308](https://github.com/thepracticaldev/dev.to/pull/2308) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-04-04T17:20:41Z">Apr 04, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2308)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构
*   [ ]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这种重构主要做三件事:

*   简化`AnalyticsService`界面
*   使用 SQL 查询而不是内存计算来检索计数
*   将日期对象用于范围

我遗漏了几件应该讨论/决定的事情:

*   `AnalyticsService`可能应该按照[@轻合金](https://dev.to/lightalloy)的建议改名为`Analytics::Pro`
*   `date.strftime("%a, %m/%d")`依赖于区域设置，可能会重复出现(如果 start_date 和 end_date 超过一年，可能会有相同字符串版本的日期)。此外，美国以外的用户可能会将日期误解为“03/04”(三月三日或四月四日？).我建议使用 ISO“2019-04-01”作为哈希的键。
*   我认为使用 regexps 的日期验证是不必要的，因为日期已经被解析了:

```
      def valid_date_params?
        date_regex = /\A\d{4}-\d{1,2}-\d{1,2}\Z/ # for example, 2019-03-22 or 2019-2-1
        if params[:end]
          (params[:start] =~ date_regex)&.zero? && (params[:end] =~ date_regex)&.zero?
        else
          (params[:start] =~ date_regex)&.zero?
        end
      end
```

Enter fullscreen mode Exit fullscreen mode

## 相关车票&单据

关闭#2307

## 手机&桌面截图/录音(如有 UI 改动)

## 添加到文档中？

*   [ ]文档开发到
*   [ ]自述文件
*   [x]不需要文档

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2308)

*   [@lightalloy](https://dev.to/lightalloy) 为用户的 GitHub 用户名和 Twitter 用户名增加了条件验证。谢谢安娜。

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

# 新的问题和讨论

*   [@glebec](https://dev.to/glebec) 提出了一个易访问性问题，在这里`diff`代码块语法高亮可以使用更易访问的颜色。谢谢， [@glebec](https://dev.to/glebec) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Markdown >代码块>` diff `-格式语法高亮:使用辅助颜色 #2087](https://github.com/thepracticaldev/dev.to/issues/2087) 

[![glebec avatar](img/082ec5e7e367ed1000b81a232739e87e.png)](https://github.com/glebec) **[glebec](https://github.com/glebec)** posted on [<time datetime="2019-03-17T22:15:17Z">Mar 17, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2087)

**您的功能请求是否与某个问题相关？请描述一下。**markdown 中语言块的`diff`语法使用红色和绿色分别作为删除行和引入行的对比色。超过 5%的男性和大约 0.5%的女性很难区分红色和绿色，这意味着一些人将很难区分这些线条，更多地依靠每行上的小`-`和`+`字符。

GitHub 中的例子(许多 UI 元素也使用红绿):

```
this is an unchanged line
- this is a removed line
+ this is a new line
this is more unchanged stuff
```

Enter fullscreen mode Exit fullscreen mode

**描述您想要的解决方案**另一种方法是修改 CSS 来突出显示差异，分别使用橙色和蓝色的变体，这种变体有更高比例的人可以区分。

**描述你考虑过的替代方案**

*   红蓝是一种不完美的折衷，比红绿好，但不如橙蓝那样普遍可辨。然而，红蓝可能更符合文化对意义的期望，总体上产生较少的混淆。
*   或者，可以不采取任何行动。这不是最容易的选择，但是只要 diffs 中仍然包含字符，它们在技术上是可读的。

**附加上下文**模型如下；确切的价值不是字面上的建议，只是精神食粮。

之前:

[![before](img/2152fe0934a80c4ca555195326fb569a.png)](https://user-images.githubusercontent.com/7230206/54498661-6c65dd00-48e0-11e9-94c0-6ffb31aca9a7.png)

之后:

[![after](img/327ef982dbd7f05d74f6fd699f09fe8c.png)](https://user-images.githubusercontent.com/7230206/54498667-725bbe00-48e0-11e9-81b9-f50a278c2501.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2087)

*   [@co16353sidak](https://dev.to/co16353sidak) 报告了一个在夜间模式下悬停在阅读列表部分时的 bug

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Bug(夜间模式):-阅读列表部分的悬停问题 #2121](https://github.com/thepracticaldev/dev.to/issues/2121) 

[![co16353sidak avatar](img/f8a7df09f60d579a8a2f71944fcdcef0.png)](https://github.com/co16353sidak) **[co16353sidak](https://github.com/co16353sidak)** posted on [<time datetime="2019-03-20T06:28:59Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2121)

**描述 bug** 在夜间模式下，页面阅读列表部分的悬停似乎有些问题。如下图所示。当 out 光标移动到阅读列表部分时，包含按钮`saved posts`和`comment activity`的下部的背景颜色意外地改变颜色，与背景颜色融合。如果我们继续将鼠标悬停在按钮上，那么整个部分的背景颜色就会完全改变，与页面融为一体。

我附上了一张 gif 来帮助理解这一点。[![Mar-20-2019 11-45-37(1)](img/accc09e250ad08a653508b2f3a87bd20.png)](https://user-images.githubusercontent.com/35738138/54663208-7832e880-4b06-11e9-8d76-4f598aa34d47.gif)

此外，如果我们与默认模式的功能进行比较，我们可以看到差异以及这应该如何工作。[![Mar-20-2019 11-46-40](img/c2c68b1fae4cefa3b17f11cceee32512.png)](https://user-images.githubusercontent.com/35738138/54663344-f5f6f400-4b06-11e9-899a-bb7913ffe6df.gif)

**重现**重现行为的步骤:

1.  启用黑暗模式
2.  转到主`dev.to`主页
3.  尝试将鼠标悬停在提到的元素上(`reading-list`部分)以查看描述的错误

**预期行为**我相信它应该像默认模式一样工作，但根据选择的模式不同，颜色变量也不同，我在默认模式下包含了相同的 gif。

**截图**以上链接。

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器:Safari 和 Firefox
*   版本:12 . 0 . 3(14606 . 4 . 5)[最新]和 67.0b3 (64 位)[最新开发者版]

**智能手机(请填写以下信息):**

(未测试)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2121)

*   [@borisschapira](https://dev.to/borisschapira) 报告了`src=/"data`导致降价错误的问题。Thansk， [@borisschapira](https://dev.to/borisschapira) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 降价码贡献错误 #2126](https://github.com/thepracticaldev/dev.to/issues/2126) 

[![borisschapira avatar](img/ded20d15eb5e31e7442cfd7d91b6a1be.png)](https://github.com/borisschapira) **[borisschapira](https://github.com/borisschapira)** posted on [<time datetime="2019-03-20T15:57:55Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2126)

**描述 bug** 无法为图片编写包含 HTML 代码的帖子。

**要复制**在一篇新文章中投稿:

```
---
title: 'Test post'
---

```
<img src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==" … 
``` 
```

**预期行为**可以保存。

**截图** [![Capture d’écran 2019-03-20 à 16 53 14](img/f9e16b8c682ec00eb12ca85eb0a8a035.png)](https://user-images.githubusercontent.com/284742/54699374-2c496900-4b31-11e9-85f6-88e37f0ce5d1.png)

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器浏览器
*   版本 73.0.3683.75

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2126)

*   [@peter](https://dev.to/peter) 请求了一个可以根据视图/反应对仪表盘进行排序的功能。谢谢彼得。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 增加了根据视图/反应对仪表盘上的文章进行排序的能力 #2134](https://github.com/thepracticaldev/dev.to/issues/2134) 

[![pkfrank avatar](img/a6281578f8aad0af41d10413c0790fd2.png)](https://github.com/pkfrank) **[pkfrank](https://github.com/pkfrank)** posted on [<time datetime="2019-03-20T21:13:15Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2134)

**您的功能请求是否与某个问题相关？请描述一下。**作为一名作者，我希望能够按照浏览量/参与度/评论数等标准对我的帖子进行排序，而不是严格按照时间顺序来查看。

**描述您想要的解决方案**仪表板上的一个选项，允许我根据特定标准(观点、反应、评论)进行筛选。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2134)

*   [@joppedc](https://dev.to/joppedc) 报告了一个问题，他们的个人资料图片在帖子的预览模式下变窄了。谢谢， [@joppedc](https://dev.to/joppedc) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 预览上压扁的个人资料图片 #2137](https://github.com/thepracticaldev/dev.to/issues/2137) 

[![JoppeDC avatar](img/593c43c3ff22d748c094b29aebe3f88e.png)](https://github.com/JoppeDC) **[JoppeDC](https://github.com/JoppeDC)** posted on [<time datetime="2019-03-20T22:28:36Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2137)

**描述 bug** 在做帖子预览时，发布者名字旁边的个人资料 pic 被压扁。

**要复制**创建一个新帖子，预览它。

1.  去写帖子
2.  点击预览
3.  查看发布者图像

**预期行为**正常图像

**截图**【https://cl.ly/8950bcf37c07】T2

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器:铬
*   版本 72

**附加内容**这一期作为第一期或第一期可能不错。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2137)

*   [@kontrollanten](https://dev.to/kontrollanten) 请求一个功能，为提要中的每个帖子在“保存”按钮内显示书签。谢谢， [@kontrollanten](https://dev.to/kontrollanten) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【保存】按钮内显示书签图标 #2144](https://github.com/thepracticaldev/dev.to/issues/2144) 

[![kontrollanten avatar](img/e063c133be02e79b378ec73059fd5a51.png)](https://github.com/kontrollanten) **[kontrollanten](https://github.com/kontrollanten)** posted on [<time datetime="2019-03-21T07:55:20Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2144)

**您的功能请求是否与某个问题相关？请描述一下。**我用 dev.to 已经有一段时间了，我很喜欢。我花了一段时间才明白书签图标是什么。很长一段时间我都以为这只是一种“反应”。所以每当我找到一个我想保存的帖子，我就必须搜索标签，然后在列表中点击“保存”按钮，以备后用。我“垃圾邮件”回复的许多其他帖子有心形、独角兽和...书签。所以我有一段时间有点迷茫。

**描述您想要的解决方案**我想更清楚地说明，在文章详情中点击书签图标与在列表视图中点击“保存”按钮具有相同的效果。

**描述你考虑过的替代方案**我想在保存按钮内添加书签按钮。我不是设计师或 UX，所以我不知道这是否有其他影响的想法。

**附加语境** [![image](img/7b05c17124460a4049f3e3725998fffe.png)](https://user-images.githubusercontent.com/6680299/54739075-05ce1100-4bb7-11e9-84e1-f0aeaf4ab20b.png)

```
 width: auto;
    min-width: auto;
    margin-right: 5px;
    height: 19px;
    margin-left: 0;
    vertical-align: -4px; 
```

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2144)

*   @rhymes 提出了一个问题，即我们的静态资产的缓存期非常短。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 静态资产拥有异常短的缓存期 #2147](https://github.com/thepracticaldev/dev.to/issues/2147) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-21T09:42:56Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2147)

**描述 bug**

我用 [Google PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fdev.to%2F) 和[网页性能测试](https://webpagetest.org/performance_optimization.php?test=190321_8H_be96eb19fc13a0947df3488adf259c64&run=1)测试了主页(两个链接都指向报告)，它们都表示静态资产(JavaScript 文件、图像和 CSS)有短期缓存，从 1 天到 7 天不等。

由于它们都有版本，我想知道是否有理由这样做，否则我会建议为它们设置更长的缓存，遵循 [Google 的最佳实践](https://developers.google.com/web/tools/lighthouse/audits/cache-policy)并将缓存设置为一年。

**预期行为**

静态资产的缓存期更长。

**截图**

Google PageSpeed Insights(与 Lighthouse 相同):

[![Screenshot_2019-03-21 PageSpeed Insights](img/f323f2a18338b6d9bd672e0471a5197d.png)](https://user-images.githubusercontent.com/146201/54743950-ec809100-4bc5-11e9-8358-94ab5da90de8.png)

网页性能测试:

[![Screenshot_2019-03-21 WebPageTest Optimization Check Results - Dulles dev to - 03 21 19 10 29 24](img/185b6d3fcbfb17045d0e496ca1ed5104.png)](https://user-images.githubusercontent.com/146201/54743969-fbffda00-4bc5-11e9-9025-8f36aa372314.png)

**桌面(请填写以下信息):**

*   操作系统:苹果操作系统
*   浏览器:Firefox
*   版本:67b3

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2147)

*   [@link2twenty](https://dev.to/link2twenty) 提出了一个关于夜间模式下特殊文字对比度的问题。谢谢， [@link2twenty](https://dev.to/link2twenty) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)# 2152](https://github.com/thepracticaldev/dev.to/issues/2152)夜间模式下的特殊文字对比

[![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2019-03-21T10:44:51Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2152)

新的 spring 横幅在夜间模式下很难阅读。有没有办法自动进行对比检查？或者我们应该在制作特殊样式时保持警惕？

[![image](img/edaf5081b440eb034217eb11b2c9f5df.png)](https://user-images.githubusercontent.com/3534427/54747199-2356a700-4bc6-11e9-8e73-7160c69c656c.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2152)

*   [@link2twenty](https://dev.to/link2twenty) 开启了主题专用 Ruby 文件的讨论。谢谢， [@link2twenty](https://dev.to/link2twenty) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 红宝石主题文件 #2154](https://github.com/thepracticaldev/dev.to/issues/2154) 

[![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2019-03-21T11:39:15Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2154)

有一个包含主题 HTML 的 ruby 文件值得吗，尽管我们现在只有夜间模式？

我对 ruby on rails 不是很了解，但我认为我们可以有一个包含多个主题的文件，但只会根据一些请求返回相关的 HTML？

CC:[@押韵](https://dev.to/rhymes)[@轻合金](https://dev.to/lightalloy)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2154)

*   [@rhymes](https://dev.to/rhymes) 提出了一个关于缺少 HTTP 严格传输安全头(HSTS)的安全问题。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加 HTTP 严格传输安全头(HSTS)  #2155](https://github.com/thepracticaldev/dev.to/issues/2155) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-21T11:56:21Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2155)

**您的功能请求是否与某个问题相关？请描述一下。**

通过玩 [dev.to 的 Mozilla Observatory](https://observatory.mozilla.org/analyze/dev.to) 的报告，我注意到该网站不发送 HSTS 标题。

TLDR；其中之一是 HSTS 报头强制客户端仅使用 HTTPS 进行连接(这不同于服务器端从 HTTP 重定向到 HTTPS)。

**描述您想要的解决方案**

我将总结一下推荐的行动计划:

1.  所有域和子域的开发。要进行检查，以确保他们都在 HTTPS 工作(即使是那些由第三方处理像商店。开发。要)
2.  逐步提高最大年龄，例如从`max-age=300; includeSubDomains`到一个月:`max-age=2592000; includeSubDomains`
3.  一旦一切正常，你可以设置推荐的两年`max-age=63072000; includeSubDomains; preload`并将域名添加到 [HSTS 预加载列表](https://hstspreload.org/?domain=dev.to)

页眉示例:

```
Strict-Transport-Security: max-age=xyz 
```

有了 Rails，它将像这样被启用:

```
config.ssl_options = { hsts: { expires: 5.minutes } }
```

Enter fullscreen mode Exit fullscreen mode

启用子域和预加载的选项[也会出现](https://api.rubyonrails.org/v5.1.6.2/classes/ActionDispatch/SSL.html)。

资源:

*   [开发到](https://observatory.mozilla.org/analyze/dev.to)的 Mozilla Observatory 结果
*   [HTTP 严格传输安全- Mozilla 安全指南](https://infosec.mozilla.org/guidelines/web_security#http-strict-transport-security)
*   [如何分阶段安全部署 HSTS](https://hstspreload.org/?domain=dev.to)
*   [HSTS 预加载和子域](https://textslashplain.com/2018/04/09/hsts-preload-and-subdomains/)
*   [铁轨上的 HSTS](https://api.rubyonrails.org/v5.1.6.2/classes/ActionDispatch/SSL.html)

**描述你考虑过的替代方案**

无所事事的:D

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2155)

*   [@link2twenty](https://dev.to/link2twenty) 提出了一个关于夜间模式下顶部导航栏通知铃声颜色的问题。谢谢， [@link2twenty](https://dev.to/link2twenty) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【主题】通知颜色 #2156](https://github.com/thepracticaldev/dev.to/issues/2156) 

[![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2019-03-21T12:51:18Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2156)

我们要为铃声主题化吗？如果是这样的话，它应该和`--theme-top-bar-write-background`一样，或者是一个新的属性，`--theme-top-bar-notification-background`？

[![image](img/56faba151204944333fd114cadc8cd37.png)](https://user-images.githubusercontent.com/3534427/54752952-a92f1e00-4bd7-11e9-8269-191a1730b230.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2156)

*   @link2twenty 也就用户如何选择主题展开了讨论。看看这个很酷的模型！再次感谢， [@link2twenty](https://dev.to/link2twenty) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【讨论】主题选择器 #2158](https://github.com/thepracticaldev/dev.to/issues/2158) 

[![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2019-03-21T13:47:52Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2158)

在某种程度上，我们将不得不谈论主题选择，我发现一个图形比一个名字更好，因为它给用户一个什么期望的想法。

我做了一个快速模型。[https://jsfiddle.net/link2twenty/3pmtew1f/](https://jsfiddle.net/link2twenty/3pmtew1f/)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2158)

*   [@aturingmachine](https://dev.to/aturingmachine) 提出了一个夜间模式的问题，帖子里的外部链接很难看到。谢谢@turingmachine！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 帖子中的外部链接在黑暗模式下很难看到 #2163](https://github.com/thepracticaldev/dev.to/issues/2163) 

[![aturingmachine avatar](img/1a8c66f41c0f8d72d00ae5aa5e3d30f1.png)](https://github.com/aturingmachine) **[aturingmachine](https://github.com/aturingmachine)** posted on [<time datetime="2019-03-21T19:40:16Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2163)

**描述 bug** 链接的颜色和文章的文字颜色差别不大。再加上深色背景改变了页面的整体对比度，这使得它们很难被看到。

**重现**重现行为的步骤:

1.  启用黑暗模式登录
2.  点击任何帖子，包括外部链接。
3.  观察链接文本颜色。

**预期行为**链接文本比文章正文更容易区分。

*   操作系统:苹果操作系统
*   浏览器浏览器
*   版本不适用

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2163)

*   @rhymes 提出了一个问题，我们应该只使用一个版本的 CodeMirror。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 只用一个版本的 CodeMirror  #2165](https://github.com/thepracticaldev/dev.to/issues/2165) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-21T20:31:38Z">Mar 21, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2165)

**您的功能请求是否与某个问题相关？请描述一下。**

代码库使用各种版本的 codemirror:

*   `package.json`声明`^5.44.0`，`yarn.lock`正在使用`5.44.0`。这是代码编辑器使用的版本:

[https://github . com/thepractical dev/dev . to/blob/a2 fcb C4 a4 D1 de 430 ff 3c 282 b 373922632 a 2d 4d/app/JavaScript/chat/code editor . jsx # L7-L8](https://github.com/thepracticaldev/dev.to/blob/a2fcbdc4a4d1de430ff3ec282b373922632a2d4d/app/javascript/chat/codeEditor.jsx#L7-L8)

*   `app/views/blocks/_form.html.erb`链接到 CodeMirror 的另一个版本，`5.26.0`:

[https://github . com/the practical dev/dev . to/blob/a2 fcb C4 a4 D1 de 430 ff 3c 282 b 373922632 a 2d 4d/app/views/blocks/_ form . html . erb # L1-L5](https://github.com/thepracticaldev/dev.to/blob/a2fcbdc4a4d1de430ff3ec282b373922632a2d4d/app/views/blocks/_form.html.erb#L1-L5)

*   `app/views/chat_channels/index.html.erb`使用第三版 CodeMirror(仅 CSS)，`5.38.0`:

[https://github . com/the practical dev/dev . to/blob/a2 fcb C4 a4 D1 de 430 ff 3c 282 b 373922632 a 2d 4d/app/views/chat _ channels/index . html . erb # L6-L7](https://github.com/thepracticaldev/dev.to/blob/a2fcbdc4a4d1de430ff3ec282b373922632a2d4d/app/views/chat_channels/index.html.erb#L6-L7)

**描述您想要的解决方案**

CodeMirror 目前处于[版本 5.45.0](https://github.com/codemirror/CodeMirror/blob/master/CHANGELOG.md#5450-2019-03-20) 。我想知道这三个部分是否可以使用来自`package.json`的相同版本

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2165)

*   [@john_papa](https://dev.to/john_papa) 提出了一个关于组织管理区夜间模式的问题。谢谢， [@john_papa](https://dev.to/john_papa) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 一个组织的管理页面需要黑色主题的对比 #2172](https://github.com/thepracticaldev/dev.to/issues/2172) 

[![johnpapa avatar](img/8ce5475456ecb663e85234fff8ab880e.png)](https://github.com/johnpapa) **[johnpapa](https://github.com/johnpapa)** posted on [<time datetime="2019-03-22T14:43:03Z">Mar 22, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2172)

注意这里的两个点都需要更多的对比度。

[![image](img/a160ef57173b101c50d0ba04bc4692d6.png)](https://user-images.githubusercontent.com/1202528/54830639-3c775a80-4c8f-11e9-93b6-160c69ca74a4.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2172)

*   [@equiman](https://dev.to/equiman) 报告了`<figcaption>`标签在夜间模式下的问题。谢谢， [@equiman](https://dev.to/equiman) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  figcaption 标签在夜间模式 #2181](https://github.com/thepracticaldev/dev.to/issues/2181) 

[![equiman avatar](img/d0c867d0a8ad83087733ed6ca1c14c7c.png)](https://github.com/equiman) **[equiman](https://github.com/equiman)** posted on [<time datetime="2019-03-24T00:14:42Z">Mar 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2181)

夜间模式下的标签颜色字体需要另一种较浅的颜色。

[![IMG_20190323_191124](img/7c97c4899094e947e4d73dc9b7331a68.png)](https://user-images.githubusercontent.com/933393/54873206-90716480-4d9f-11e9-9223-934d28ae6fcd.jpg)

[![IMG_20190323_191128](img/26411f555e8c0644f8ba6e5c072c19ca.png)](https://user-images.githubusercontent.com/933393/54873208-9404eb80-4d9f-11e9-8844-77ac080ce8d4.jpg)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2181)

*   [@equiman](https://dev.to/equiman) 报告了仪表板中草稿帖子的夜间模式问题。谢谢， [@equiman](https://dev.to/equiman) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 夜间模式 #2182](https://github.com/thepracticaldev/dev.to/issues/2182) 

[![equiman avatar](img/d0c867d0a8ad83087733ed6ca1c14c7c.png)](https://github.com/equiman) **[equiman](https://github.com/equiman)** posted on [<time datetime="2019-03-24T00:18:51Z">Mar 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2182)

仪表板屏幕上的草稿文章在夜间模式下更亮

[![Screenshot_20190323-191607](img/2bbc2350fe98373673ed6d9bd9ec4ed4.png)](https://user-images.githubusercontent.com/933393/54873233-3d4be180-4da0-11e9-93b4-cbcaff1855d3.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2182)

*   [@equiman](https://dev.to/equiman) 报告了个人资料页面中社交图标的夜间模式问题。再次感谢， [@equiman](https://dev.to/equiman) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 社交图标 #2183](https://github.com/thepracticaldev/dev.to/issues/2183) 

[![equiman avatar](img/d0c867d0a8ad83087733ed6ca1c14c7c.png)](https://github.com/equiman) **[equiman](https://github.com/equiman)** posted on [<time datetime="2019-03-24T00:22:36Z">Mar 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2183)

个人资料中的社交图标颜色不同。

[![IMG_20190323_192118](img/fcc339cac72d92117bf4c1e50b458ac5.png)](https://user-images.githubusercontent.com/933393/54873270-fe6a5b80-4da0-11e9-8827-90b246a6cb5d.jpg)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2183)

*   [@equiman](https://dev.to/equiman) 报告了您仪表板中“关注标签”部分的夜间模式问题。再次感谢， [@equiman](https://dev.to/equiman) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在夜间模式下跟随标签 #2184](https://github.com/thepracticaldev/dev.to/issues/2184) 

[![equiman avatar](img/d0c867d0a8ad83087733ed6ca1c14c7c.png)](https://github.com/equiman) **[equiman](https://github.com/equiman)** posted on [<time datetime="2019-03-24T00:29:37Z">Mar 24, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2184)

开启夜间模式。以下标签和文字为浅色。

[![Screenshot_20190323-192459](img/504a2099677de970d53ad6f33f59e240.png)](https://user-images.githubusercontent.com/933393/54873311-aa13ab80-4da1-11e9-9387-bf9b7efd1c23.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2184)

*   [@rhymes](https://dev.to/rhymes) 开启了关于未来 REST API 中是否应该使用随机生成 id 的讨论。谢谢，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 使用 REST API 中随机生成的 id# 2187](https://github.com/thepracticaldev/dev.to/issues/2187)

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-25T00:00:30Z">Mar 25, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2187)

**您的功能请求是否与某个问题相关？请描述一下。**

我是在阅读了[这篇关于 dev.to 的文章](https://dev.to/daolf/-what-is-the-best-time-to-post-on-devto-a-data-backed-answer--1kob)中的数据分析的精彩文章后开出这张罚单的。

在为 API 的未来“发布”做准备时，我想提出的问题是:开发人员是否应该使用随机生成的资源 id，而不是公开“内部”自动递增的整数主键？

API 设计中的一个“经典”最佳实践是不公开应用程序的内部细节，尽可能将数据的组织方式与第三方的访问方式分开。

公共 API 中通常不鼓励自动递增主键(有些人，不是所有人)，主要有三个原因:

1.  它们向公众公开数据库 id。今天您可能在关系数据库中有数据，明天这些数据可能在其他地方，这些 id 可能没有多大意义，因为自动递增主键对于一个数据源来说是本地的。
2.  通过暴露递增的 id，旁观者(或恶意攻击者)可以收集两个信息:表的大小和增长率
3.  恶意攻击者更容易编写脚本来抓取数据

这与其说是技术上的争论，不如说是政策上的问题，因为，至少对于三个原因中的第二个，DEV 可能对此完全没有意见。

自动递增整数主键的一些可能的替代方法:

*   uuid:PostgreSQL 有一个[本机可索引 UUID 类型](https://www.postgresql.org/docs/9.6/datatype-uuid.html)，它具有生成默认 uuid 的函数，就像 pgcrypto 的`gen_random_uuid()`。虽然 UUIDs 是不可排序的，但是它们看起来很难看，而且很冗长:`https://dev.to/api/articles/2d931510-d99f-494a-8c67-87feb05e1594`
*   KSUIDs:它们是[相当新的](https://segment.com/blog/a-brief-history-of-the-uuid/)，是由 Segment“发明”的。它们的特点是可排序和时间相关:`https://dev.to/api/articles/0ujsszwN8NRY24YaXiTIE2VWDTS`
*   应用程序中生成的一些其他随机字母数字 ID(如 Ruby 的`SecureRandom.alphanumeric(20)` ): `https://dev.to/api/articles/cDAkKyz38cqjTI1bV5lG`

我倾向于最后两个选项中的一个

**描述您想要的解决方案**

因为 API 还没有公开，而且它相对较小(只有 8 个控制器)，我认为如果在这一点上有共识的话，过渡是可行的。出版和文档之后可能会更难，特别是因为 DEV 已经是一个很大的社区，开发人员已经抓住了使用 API 的机会(因此有了这篇文章；))

由于显而易见的原因，不应该出现两者(整数 id 和字母数字 id)同时工作的情况。这只会给系统带来压力(因为它可能导致两个查询，而不是一个)。

前端/SPA 也需要调整和更新。

你怎么想呢?

**附加上下文**

我快速浏览了一些公共 API(从[开始，即 DEV](https://docs.dev.to/addl-tech/) 自己使用的 API，这是我的发现:

*   [快速地](https://docs.fastly.com/api/purge)使用 URL(出于显而易见的原因)和字母数字标识(例如`SU1Z0isxPaozGVKXdv0eY`)来标识资源，可能是由 Ruby 的`SecureRandom.alphanumeric(21)`的等价物生成的
*   [Cloudinary](https://cloudinary.com/documentation/upload_images#public_id_the_image_identifier) 使用一个随机生成的字符串`public_id`(例如`8jsb1xofxdqamu2rzwt9q`)
*   [Stripe](https://stripe.com/docs/api/charges/object) 使用以资源标识符为前缀的字母数字随机字符串，例如`ch_19yUdh2eZvKYlo2CkFVBOZG7`代表费用，`cus_El7v7DRE34iBPx`代表客户

还有几个:

*   [StackOverflow](https://api.stackexchange.com/docs/types/answer) 使用整数 id
*   类似于 Stripe，Twilio 使用带有资源类型前缀的字符串 id
*   [PayPal](https://developer.paypal.com/docs/api/orders/v2/#orders_get) 使用字母数字标识

参考文献 911

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2187)

*   [@kenbellows](https://dev.to/kenbellows) 报告了一个问题，某些字符，如尖括号，在某些文章预览中显示不正确。谢谢， [@kenbellows](https://dev.to/kenbellows) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 某些文字，如尖括号，在某些文章预览中出现错误 #2204](https://github.com/thepracticaldev/dev.to/issues/2204) 

[![kenbellows avatar](img/cfacded4ecfce7909bb0b3a3465027b9.png)](https://github.com/kenbellows) **[kenbellows](https://github.com/kenbellows)** posted on [<time datetime="2019-03-25T15:58:06Z">Mar 25, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2204)

**描述 bug** 在查看一篇文章的`/comments`视图时，显示的是文章的第一行半左右。如果第一位包含带尖括号的内联代码，例如，如果您编写:

```
this is an article about `<span>` tags 
```

这在文章预览中显示为:“这是一篇关于标签的文章”。

**重现**重现行为的步骤:

1.  转到'[https://dev.to/new](https://dev.to/new)'。
2.  输入最小封面内容，设置“已发布:真”,然后输入:

```
this is an article about `<span>` tags 
```

3.  保存文章。
4.  导航至{文章 URL }/评论。
5.  观察预览文本中的`&lt;span&gt;`。

**预期行为**我预计预览会说“这是一篇关于`<span>`标签的文章”

**截图**示例来自:[https://dev . to/ken bellows/stop-using-so-many-divs-an-intro-to-semantic-html-3i9i/comments](https://dev.to/kenbellows/stop-using-so-many-divs-an-intro-to-semantic-html-3i9i/comments)[![Screen Shot 2019-03-25 11:45 AM EST](img/f6d6a87e4a7699463e129ba6c82bc259.png)](https://user-images.githubusercontent.com/657118/54933747-fbd04900-4efb-11e9-9e0d-db34b0a922bd.png)

**桌面(请填写以下信息):**

*   操作系统:macOS Mojave 10.14.3
*   浏览器:Chrome 72.0.3626.121，Opera 58.0.3135.107

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2204)

*   [@john_papa](https://dev.to/john_papa) 报告了夜间模式关于注释中代码如何需要对比的问题。谢谢， [@john_papa](https://dev.to/john_papa) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 黑暗模式-评论中的代码需要对比 #2210](https://github.com/thepracticaldev/dev.to/issues/2210) 

[![johnpapa avatar](img/8ce5475456ecb663e85234fff8ab880e.png)](https://github.com/johnpapa) **[johnpapa](https://github.com/johnpapa)** posted on [<time datetime="2019-03-26T01:47:09Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2210)

见下文

[![image](img/aea6804b8e1491ba4984bee70d6f6053.png)](https://user-images.githubusercontent.com/1202528/54965579-82f6de80-4f47-11e9-9231-c80a91e778f6.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2210)

*   [@learnbyexample](https://dev.to/learnbyexample) 报告了一个问题，字符串包含⭕的代码块被更改为表情符号。谢谢， [@learnbyexample](https://dev.to/learnbyexample) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 字符串中包含⭕的代码块变为⭕️  #2216](https://github.com/thepracticaldev/dev.to/issues/2216) 

[![learnbyexample avatar](img/eb4c738a621bcc82c26b6ffc55982654.png)](https://github.com/learnbyexample) **[learnbyexample](https://github.com/learnbyexample)** posted on [<time datetime="2019-03-26T13:00:26Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2216)

**描述 bug**

字符串包含`:o:`(冒号包围的`n`后面的小写字母)的代码块被改为`⭕️`(可能是表情符号？)

**重现**

在帖子/评论中使用这些代码块中的任何一个，并预览它就显示了问题。据我所知，这个序列中有没有其他字符并不重要。

```
```
:o:
```

```python
':o:'
```

```ruby
':o:'
":o:"
``` 
```

**预期行为**

在代码块中，我不期望任何字符串组合(据我所知)被解释和转换成另一个字符串。`:o:`应该只是`:o:`而不是`⭕️`

**截图**

上面的代码块产生以下输出:

[![code_block_issue](img/58bf45933616b3891d349820257376ee.png)](https://user-images.githubusercontent.com/17766317/54998185-85167700-4ff3-11e9-9f3c-3f2d5cd8b9c1.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2216)

*   [@healeycodes](https://dev.to/healeycodes) 报告了一个夜间模式的问题，嵌入的评论很难阅读。感谢 [@healeycodes](https://dev.to/healeycodes) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  Bug(夜间模式):内嵌评论难读 #2221](https://github.com/thepracticaldev/dev.to/issues/2221) 

[![healeycodes avatar](img/7a45c36d264daf77d0060120cd82d851.png)](https://github.com/healeycodes) **[healeycodes](https://github.com/healeycodes)** posted on [<time datetime="2019-03-26T18:19:17Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2221)

**描述 bug** 开启夜间模式，文章中嵌入的评论很难阅读。

它们在深蓝色背景下显示为黑色文本。

**要在启用夜间模式的情况下重现**，请访问嵌入评论的文章。

如[https://dev . to/dev team/top-5-dev-comments-from-the-past-week-gia](https://dev.to/devteam/top-5-dev-comments-from-the-past-week-gia)

**截图**

[![An embedded comment](img/403783683d807007a3ac57ce50bff138.png)](https://user-images.githubusercontent.com/34559231/55022288-2b617d00-4ff2-11e9-8471-4d6996801291.png)

**桌面:**

*   操作系统:Windows 10 操作系统内部版本号 17134.648
*   浏览器:铬
*   版本:73.0.3683.86(官方版本) (64 位)(群组:稳定)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2221)

*   [@andypiper](https://dev.to/andypiper) 报告了一个夜间模式的问题，组织的 CTA 框内容很难读取。谢谢， [@andypiper](https://dev.to/andypiper) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 黑暗模式- org CTA 框内容难以阅读(需要对比) #2222](https://github.com/thepracticaldev/dev.to/issues/2222) 

[![andypiper avatar](img/509fe116a0ff9c012ef2a4157a4e5d05.png)](https://github.com/andypiper) **[andypiper](https://github.com/andypiper)** posted on [<time datetime="2019-03-26T20:42:41Z">Mar 26, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2222)

**描述 bug** 很难看清组织帖子上 CTA 框中深色背景下的黑色文字。

**重现**重现行为的步骤:

1.  前往[https://dev.to/twilio](https://dev.to/twilio)
2.  点击任何帖子
3.  检查侧边栏
4.  见下文

**截图** [![Screenshot 2019-03-26 at 20 40 43](img/ddfa38a648eec80fdb76201e28abb305.png)](https://user-images.githubusercontent.com/552452/55031939-ac773f00-5007-11e9-9e77-718ac4e9ed29.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2222)

*   [@lito](https://dev.to/lito) 请求了一个特性，我们用当前部署的 git 提交散列添加一个 HTML `<meta>`标签。谢谢， [@lito](https://dev.to/lito) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加一个部署了 git 提交散列的 HTML Meta 标签 #2223](https://github.com/thepracticaldev/dev.to/issues/2223) 

[![eusonlito avatar](img/7a54ef3a3748d2fde55804cac587546a.png)](https://github.com/eusonlito) **[eusonlito](https://github.com/eusonlito)** posted on [<time datetime="2019-03-27T11:53:44Z">Mar 27, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2223)

在部署了当前 git 提交的情况下，在 header 上添加一个`html` `meta`标记怎么样？

通过这种方式，与 github 提交相比，贡献者和开发者很容易跟踪网站状态和部署。

我不知道这个提议是否有某种标签，但是像这样的东西怎么样:

```
<meta name="git-commit" content="6e23483b9421c1e1a03d94093306badf277cc19e"> 
```

问候:)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2223)

*   [@lito](https://dev.to/lito) 报告了标签中删除非英文字符的问题。再次感谢， [@lito](https://dev.to/lito) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 标签上非英文字符被删除 #2226](https://github.com/thepracticaldev/dev.to/issues/2226) 

[![eusonlito avatar](img/7a54ef3a3748d2fde55804cac587546a.png)](https://github.com/eusonlito) **[eusonlito](https://github.com/eusonlito)** posted on [<time datetime="2019-03-27T19:28:57Z">Mar 27, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2226)

**描述 bug** 当你用一些非英文字符写标签时，这些字符会在文章视图中被删除。

**重现**

1.  使用类似`Optimización`(优化)的标签。
2.  救援
3.  转到文章
4.  `Optimización`现在是`optimizacin`

**预期行为**显示`Optimización`如同在 markdown 编辑器中。

**截图** [![Tags Bug](img/30d9d4347093dbfd56065061069c172a.png)](https://user-images.githubusercontent.com/644551/55106274-d0ea1e80-50ce-11e9-8a61-8213f1da75e6.jpg)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2226)

*   [@brylie](https://dev.to/brylie) 报告了一个服务人员和隐私扩展阻止图片加载的问题。谢谢， [@brylie](https://dev.to/brylie) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 加载图片错误 #2228](https://github.com/thepracticaldev/dev.to/issues/2228) 

[![brylie avatar](img/ed6f8a4572f3182f944fbcbb8dcda2ff.png)](https://github.com/brylie) **[brylie](https://github.com/brylie)** posted on [<time datetime="2019-03-27T22:05:50Z">Mar 27, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2228)

我在 Reddit 上分享了一篇 dev.to 文章，其中一位评论者说图片无法加载。

以下是他们诊断的其余部分:

```
These images:

https://i.imgur.com/GArpxyC.png

Errors reported in console:

manifest-36a1d0b68d1e3664106b.js:1 Uncaught SyntaxError: Unexpected token <

vendor-a16759536b738a97f545.js:1 Uncaught SyntaxError: Unexpected token <

Search-d6be81c747f6325b6aae.js:1 Uncaught SyntaxError: Unexpected token <

base-55719a78ce4c394706e03834ae49c59cddc235c3ef5dbc299c63b4edf0f0b558.js:1 Uncaught SyntaxError: Unexpected token <

webShare-1698d2d09639b4c8b299.js:1 Uncaught SyntaxError: Unexpected token <

reload-router-view-when-vue-route-changes-3cck:1 Error while trying to use the following icon from the Manifest: https://practicaldev-herokuapp-com.freetls.fastly.net/assets/devlogo-pwa-192-b92c1c02f2f63967c2889ba04a4d7bc61da640a6dbe419d65703b1282c96b46c.png (Download error or resource isn't a valid image)

serviceworker.js:1 Uncaught (in promise) TypeError: Failed to fetch

If I go to "Google chrome dev tools" --> "Application" --> "Service worker" and check "Bypass for network", and reload, everything works

After some more debugging, it seems like the XHR requests to *.freessl.fastly are blocked by the privacy settings in my browser, if I whitelist that origin for XHR requests from your domain, it works 
```

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2228)

*   [@lightalloy](https://dev.to/lightalloy) 提出了合并用户账号应该在数据库事务中完成的问题。谢谢安娜。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 合并用户账户应在一次交易中完成【2230 号](https://github.com/thepracticaldev/dev.to/issues/2230)

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-03-28T08:26:07Z">Mar 28, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2230)

我认为为了更安全的合并，至少一些操作应该在 db 事务内部完成。

[https://github . com/thepractical dev/dev . to/blob/7c 1418 a 87 c 654 C2 feb 56124 c 2f 750 ee5a 8455 f 12/app/services/moderator/merge _ user . Rb # L15-L26](https://github.com/thepracticaldev/dev.to/blob/7c1418a87c654c2feb56124c2f750ee5a8455f12/app/services/moderator/merge_user.rb#L15-L26)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2230)

*   [@ben](https://dev.to/ben) 请求了一个我们 v2 editor 也可以处理普通正面问题的特性。谢谢你，本！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)【v2 内切换平原前沿编辑 #2234](https://github.com/thepracticaldev/dev.to/issues/2234) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-28T14:03:32Z">Mar 28, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2234)

**您的功能请求是否与某个问题相关？请描述一下。目前，如果你想写“简单的”markdown，你可以用原来的编辑器，这很好，但总是有点奇怪，偶尔会有错误。**

新的编辑器有丰富的 UI 控件，但是有些人仍然想要 markdown 粘贴体验(除了品味之外还有很多有效的理由！)

如果“v2”(preact 构建的编辑器)支持我们的整个用例范围，并且我们可以淘汰旧代码(可能由社区维护)，那就太好了

**描述您想要的解决方案**

任何可以在编辑器类型之间切换的区域都很好。用户可以有一个默认的编辑器类型，但需要时可以轻松切换。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2234)

*   @antonrich 请求了一个可以在搜索查询中组合标签的功能。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 在搜索查询中将标签组合在一起。 #2238](https://github.com/thepracticaldev/dev.to/issues/2238) 

[![AntonRich avatar](img/f363cf365fe89b1152a68d1527f071e7.png)](https://github.com/AntonRich) **[AntonRich](https://github.com/AntonRich)** posted on [<time datetime="2019-03-29T13:53:49Z">Mar 29, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2238)

如果用户可以编写以下格式的搜索查询:#tag1 #tag2，并收到实际上有这种标签的结果，那就太好了。

[https://dev . to/Anton rich/how-can-I-combine-tags-when-search-on-dev-to-5b 19](https://dev.to/antonrich/how-can-i-combine-tags-when-searching-on-dev-to-5b19)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2238)

*   [@mte90](https://dev.to/mte90) 使用用户选择的标签改进订阅源

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 使用用户选择的标签改进 feed# 2249](https://github.com/thepracticaldev/dev.to/issues/2249)

[![Mte90 avatar](img/6d7c90e92facfd623fde6c6cf4d7a361.png)](https://github.com/Mte90) **[Mte90](https://github.com/Mte90)** posted on [<time datetime="2019-03-31T15:57:03Z">Mar 31, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2249)

**描述 bug** 一个用户选择想要关注的标签，但是在各种可用的 feed 上没有任何优先级。

**重现**重现行为的步骤:

1.  为您的用户设置标签
2.  检查你订阅源中文章的标签

**预期行为**订阅源只有用户正在关注的标签内容，否则它们现在毫无用处

**截图** [![immagine](img/d7be91fc2e3e6b9a7f7fef504d0ac486.png) ](https://user-images.githubusercontent.com/403283/55291366-2def1880-53de-11e9-987d-6ee05c16481f.png) [ ![immagine](img/68c2ee7e7450661162733b85cb91bade.png)](https://user-images.githubusercontent.com/403283/55291374-3a737100-53de-11e9-835c-df3469a0a66a.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2249)

*   [@rhymes](https://dev.to/rhymes) 报告了一个奇怪的 API 问题，其中`https://dev.to/api/comments?a_id=some_id`无法工作，尽管相关代码似乎没有任何问题。感谢举报，[@韵](https://dev.to/rhymes)！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  API:无法获取一篇文章的评论 #2250](https://github.com/thepracticaldev/dev.to/issues/2250) 

[![rhymes avatar](img/8ee01b54667589ec37e5f81bb83090e3.png)](https://github.com/rhymes) **[rhymes](https://github.com/rhymes)** posted on [<time datetime="2019-03-31T16:52:02Z">Mar 31, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2250)

**描述 bug**

我似乎无法从 DEV API 中检索文章评论。它适用于我的本地安装，但不使用实时 API。

给定文章 API 响应[https://dev.to/api/articles/95907](https://dev.to/api/articles/95907)-[https://dev . to/Kathy ra _/what-security-through-obscency-is-and-why-it-s-evil-47 D5](https://dev.to/kathyra_/what-security-through-obscurity-is-and-why-it-s-evil-47d5)——我试图使用[https://dev.to/api/comments?a_id=95907](https://dev.to/api/comments?a_id=95907)检索它的评论，但服务器返回了`HTTP 404`。

根据代码，乍一看它应该可以工作:

[https://github . com/thepractical dev/dev . to/blob/54 cef 47669759 b 060370 C1 CDE 8 f 527 ce 6d 39333/app/controllers/API/v 0/comments _ controller . Rb # L15-L18](https://github.com/thepracticaldev/dev.to/blob/54ccef47669759b060370c1cde8f527ce6d39333/app/controllers/api/v0/comments_controller.rb#L15-L18)

奇怪的是，这种调用在本地安装上工作正常。

我可能做错了什么。

**重现**重现行为的步骤:

1.  前往[https://dev.to/api/comments?a_id=95907](https://dev.to/api/comments?a_id=95907)
2.  参见错误

**预期行为**

我希望这个端点返回 id 为`95907`的文章的所有评论，应该如下:[https://dev . to/Kathy ra _/what-security-through-obscurity-is-and-why-it-s-evil-47 D5](https://dev.to/kathyra_/what-security-through-obscurity-is-and-why-it-s-evil-47d5)

**截图**

[![Screenshot 2019-03-31 at 6 50 42 PM](img/9e7fdd4320170828cd9dc031c8bca66c.png)](https://user-images.githubusercontent.com/146201/55292018-f1271f80-53e5-11e9-81be-d8954c52ead6.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2250)

*   [@mjraadi](https://dev.to/mjraadi) 报告了夜间模式下评论预览文本不可读的问题。谢谢， [@mjraadi](https://dev.to/mjraadi) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【夜间模式】:文章评论预览需修复 #2286](https://github.com/thepracticaldev/dev.to/issues/2286) 

[![mjraadi avatar](img/4244e10cdc945dace6882cda739d9b26.png)](https://github.com/mjraadi) **[mjraadi](https://github.com/mjraadi)** posted on [<time datetime="2019-04-02T10:17:16Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2286)

**描述 bug** 一篇文章的评论预览需要根据夜间模式配色进行更新。背景颜色为黄色，字体颜色为白色，文本不可读。

**重现**重现行为的步骤:

1.  转到任何文章
2.  在注释框中键入任何内容
3.  点击“预览”
4.  看到行为了吗

**预期行为**背景颜色和文本颜色之间的对比。

**截图** [![Screenshot_20190402_144338](img/5e100bf47171eb505e3506ba492851aa.png)](https://user-images.githubusercontent.com/20579660/55394990-e4c1d480-5555-11e9-818e-3740ee057274.png)

**桌面(请填写以下信息):**

*   操作系统:Arch Linux Manjaro
*   浏览器浏览器

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2286)

*   [@learnbyexample](https://dev.to/learnbyexample) 报告了一个问题，如果是嵌套项目符号列表的一部分，带有双反斜杠的内联代码会变成单反斜杠。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 如果是嵌套项目符号列表  #2288](https://github.com/thepracticaldev/dev.to/issues/2288) 的一部分，带双反斜杠的内联代码变成单个

[![learnbyexample avatar](img/eb4c738a621bcc82c26b6ffc55982654.png)](https://github.com/learnbyexample) **[learnbyexample](https://github.com/learnbyexample)** posted on [<time datetime="2019-04-02T11:07:38Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2288)

**描述 bug**

嵌套清单中代码块内的双反斜杠导致单反斜杠而不是双反斜杠。

**重现**

在任何帖子/评论中，尝试这些非嵌套和嵌套的项目符号列表:

```
* foo
* use `\\1`, `\\2`, etc

---

* foo
    * baz
* use `\\1`, `\\2`, etc 
```

**预期行为**

双反斜杠不应该变成单反斜杠，如下图 GitHub markdown 渲染所示:

*   富（中国姓氏）
    *   巴兹
*   使用`\\1`、`\\2`等

**截图**

上述非嵌套(正确呈现)和嵌套列表(不正确)的屏幕截图:

[![nested_bullet_double_backslash](img/97c1f06639ac0bc3892166038b0753f5.png)](https://user-images.githubusercontent.com/17766317/55397518-088c1700-5564-11e9-8203-50386f196e05.png)

不确定这是否与:[https://github.com/thepracticaldev/dev.to/issues/960](https://github.com/thepracticaldev/dev.to/issues/960)有关

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2288)

*   [@jess](https://dev.to/jess) 报告了一个代码沙盒液体标签 w/ focus 输入导致页面跳转的问题。谢谢杰西。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 代码沙盒液体标签 w/焦点输入导致页面滚动加载 #2290](https://github.com/thepracticaldev/dev.to/issues/2290) 

[![jessleenyc avatar](img/22db1a894eb262344cf13b7781b35cdd.png)](https://github.com/jessleenyc) **[jessleenyc](https://github.com/jessleenyc)** posted on [<time datetime="2019-04-02T14:50:04Z">Apr 02, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2290)

**描述一个用户报告的错误**，该用户试图嵌入一个关注输入的 codesandbox。加载时，页面“跳转”/滚动到嵌入位置。

**要复制**创建一个新的贴 w/ this liquid 标签:`{% codesandbox pyqq0o9mk7 %}`。确保它出现在折叠线下方，以便您可以看到跳跃。

**预期行为** Liquid 标签不应导致页面加载滚动。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2290)

*   [@forsh3y](https://dev.to/forsh3y) 开启了关于夜间模式字体粗细的讨论。谢谢， [@forsh3y](https://dev.to/forsh3y) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【暗模式】文字粗细/字距【2295 号](https://github.com/thepracticaldev/dev.to/issues/2295)

[![forsh3y avatar](img/64f8859de72118d2859b959da81e535e.png)](https://github.com/forsh3y) **[forsh3y](https://github.com/forsh3y)** posted on [<time datetime="2019-04-03T01:53:54Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2295)

**您的功能请求是否与某个问题相关？请描述一下。**我没有视力障碍，但是在夜间模式下阅读大量文本会让我的眼睛受伤！

**描述您想要的解决方案**在深色模式下为常规文本使用较轻的字体粗细。[详情如下:](https://css-tricks.com/dark-modes-with-css/#article-header-id-2)

**描述你考虑过的替代方案**不确定，我想这无论如何都是很远很远的事。

**附加上下文**这展示了字体粗细是如何难以区分以及文本是如何“聚集”的。【T2![image](img/501d52b135afb7b60f6a90579e1a27e3.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2295)

*   @jaydm 发起了一个关于让阅读时间成为‘选择加入’功能的讨论。谢谢， [@jaydm](https://dev.to/jaydm) ！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 考虑让阅读时间成为一个“选择加入”功能 #2304](https://github.com/thepracticaldev/dev.to/issues/2304) 

[![jaydm avatar](img/f2aac32fbd286dff22576cad722ea28b.png)](https://github.com/jaydm) **[jaydm](https://github.com/jaydm)** posted on [<time datetime="2019-04-03T22:06:13Z">Apr 03, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/2304)

**您的功能请求是否与某个问题相关？请描述一下。**有阅读障碍的人可能会觉得阅读时间不愉快: [![image](img/b94cbd02009cf2053f130d64edd17f79.png)](https://user-images.githubusercontent.com/595062/55516478-f1682a80-5632-11e9-8a67-0ff40e13562b.png)

**描述您想要的解决方案**建议取消阅读时间，或者提供一个选择加入以查看它们的选项(这样默认情况下它们是隐藏的)。

**描述你考虑过的替代方案**见上文——要么取消阅读时间，要么提供一个选择加入功能(cookie？)

**附加语境**这里有一篇显示阅读时间的博文: [![image](img/b7fd04d2d53ec848083af15967a2a00f.png)](https://user-images.githubusercontent.com/595062/55516417-c41b7c80-5632-11e9-9fd2-629a645d4904.png)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/2304)

*   [@rhymes](https://dev.to/rhymes) 开启了一场关于处理我们的分析数据的可能改进和想法的讨论。谢谢，[@韵](https://dev.to/rhymes)！

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

### 利弊

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

# 开发-iOS

我们最近没有任何新的问题或 PRs 合并。请随意查看 [iOS 回购](https://github.com/thepracticaldev/dev-ios)，或者在 App Store 下载[我们的 iOS 应用。](https://itunes.apple.com/us/app/dev-community/id1439094790)

这星期到此为止！下周，我保证我们将涵盖 4 月 6 日至 4 月 12 日，并在每周定期安排。🙇‍♂️**