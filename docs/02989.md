# 在云☁中管理数据

> 原文：<https://dev.to/azure/managing-data-in-the-cloud-27mm>

#### 了解如何使用云服务对数据进行分类和托管。

2019 年 5 月 13 日，星期一，是我们开始首届[的日子。网南大会](https://dotnetsouth.tech)。也是在那天，我发表了一个新的演讲，“在云端管理数据”

[![](img/477c96bfdf789d421286e37db25d8b8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WpjXHabN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9K3vUSo20I00CPPqBCFbKw.png)

会议被记录下来，当它可用时，我会用链接更新这篇文章。

本演示以三个案例研究为特色，展示了管理和访问数据的不同方法。有一个航班数据库，它遍历节点和顶点的图来计算两个机场之间可能的飞行路径。链接缩短示例突出了我在生产中运行的完全无服务器的应用程序，用于创建、使用和报告 URL。最后，Tailwind Traders 应用程序利用了微服务，其中包括一个 Node.js 应用程序，该应用程序具有 MongoDB 后端和一个基于 Azure SQL 的. NET 核心 API。

> ![unknown tweet media content](img/8cbdf453f22740e091045ccf59f138ef.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop=""><source src="https://video.twimg.com/amplify_video/1127933673813282816/vid/852x480/PyVLMa-i8SWi37Sf.mp4?tag=12" type="video/mp4"></video>![Jeremy Likness ⚡️ profile image](img/d04887d50b7626acca9def32d5697006.png)Jeremy likness⚡️@ Jeremy likness![twitter logo](img/4d9c44713c216584b3d48ff3455cbb68.png)感谢所有参加我的 [@DotNetSouth](https://twitter.com/DotNetSouth) 会话“管理数据📈《云中☁.》正如所承诺的，这是🐱包含完整演示、源代码、分步演示说明、文档和学习模块链接的存储库。[jlik.me/fvm](https://t.co/Bslp3O5XM4)2019 年 5 月 13 日下午 17:23[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1127987856130101253)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1127987856130101253)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1127987856130101253)0

该演示包含所有相关链接，并介绍了如何对数据进行分类，每类数据如何转换为云服务，以及如何构建应用程序以使用各种选项。本资料最后介绍了一项旨在将您的数据从现有内部解决方案迁移到云的服务。

💾[下载演示文稿](https://jlikme.blob.core.windows.net/presentations/Likness-ManagingCloudData-DotNetSouth.pptx)

存储库甚至更深。它包含运行演示的先决条件的链接。它包括每个演示的分步说明。最后，还有针对所涵盖的所有服务的动手学习模块和文档的链接。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [耶路撒冷](https://github.com/JeremyLikness) / [管理云数据](https://github.com/JeremyLikness/managing-cloud-data)

### 关于管理云数据演示的演示助手

<article class="markdown-body entry-content p-5" itemprop="text">

# 管理云中的数据

这是演示预演和“在云中管理数据”演示的资源

<g-emoji class="g-emoji" alias="link" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f517.png">🔗</g-emoji>在这里下载[的 PowerPoint 演示文稿。](https://jlikme.blob.core.windows.net/presentations/Likness-ManagingCloudData-DotNetSouth.pptx)

## 先决条件

*   Azure 订阅。没有吗？[免费获得一个](https://jlik.me/fsj)
*   [Azure 存储模拟器](https://jlik.me/fsk)
*   [Azure 存储浏览器](https://jlik.me/fsn)
*   [Visual Studio](https://jlik.me/fso)

## 创建存储帐户

<g-emoji class="g-emoji" alias="woman_student" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f469-1f393.png">👩‍🎓</g-emoji> <g-emoji class="g-emoji" alias="man_student" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f468-1f393.png">👨‍🎓</g-emoji> [了解如何创建存储账户](https://jlik.me/ftn)

1.  创建新的存储帐户
2.  将其放在新的资源组中
3.  对 V2 和 RA-GRS 以及“热”使用默认值
4.  创造

### 查看功能

1.  导航到“Blobs”
2.  导航到“表格”
3.  导航到“文件”
4.  点击 <g-emoji class="g-emoji" alias="heavy_plus_sign" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2795.png">➕</g-emoji> 文件共享
5.  显示选项

## Blob 存储演示

<g-emoji class="g-emoji" alias="woman_student" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f469-1f393.png">👩‍🎓</g-emoji> <g-emoji class="g-emoji" alias="man_student" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f468-1f393.png">👨‍🎓</g-emoji> [了解 blob 存储](https://jlik.me/fto)

从上一步创建的帐户开始。

1.  创建一个名为“图像”的容器
2.  将访问级别设置为“Blob”
3.  显示容器属性
4.  导航至“访问策略”
5.  在“存储的访问策略”下显示“添加策略”
6.  [从高层次解释情景应用程序](https://jlik.me/ftp)
7.  …

</article>

[View on GitHub](https://github.com/JeremyLikness/managing-cloud-data)

如果您正在探索将数据迁移到云或者构建新的解决方案，并且想知道有哪些选项可用，您可能会发现这些资源很有帮助。尽情享受吧！

问候，

[![](img/97adde39a566ae152324f60361115585.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u6zXBAsP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/197/1%2AKwXn3ElS7vnWIk8lNVhBsA.gif)

* * *