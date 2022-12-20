# 前端新闻#1: Mozilla 插件宕机，MS Edge 新功能，2019 年 JS 趋势等等

> 原文：<https://dev.to/adriansandu/front-end-news-1-mozilla-add-on-outage-ms-edge-new-features-js-trends-in-2019-and-more-4f12>

[![](img/1dba683c53cb958fa219e6b47a378f63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yCmB40h8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArFvs3XWDPk-SVFuK6EDD5Q.png)

大家好，欢迎收看前端新闻第一集。我开始这一系列的文章(和视频)来汇总上周影响前端开发行业的最重要的事件。

今天的头条新闻是 5 月 3 日 Mozilla 插件宕机事件的后续报道。我们将继续介绍基于 Chromium 的新 Edge 浏览器中内置的更多细节，我们将了解 2019 年的 JavaScript 趋势，CodePen 将发布新闻和更新，我们还将推出新的手册来阅读 Web 项目指南。

我还制作了内容的视频版本，您可以观看(和收听):

[https://www.youtube.com/embed/pAASN1IYuao](https://www.youtube.com/embed/pAASN1IYuao)

让我们开始吧…

### **对 Mozilla 插件宕机的影响**

5 月 3 日，全世界所有的火狐用户都有一个不愉快的惊喜，他们发现他们的插件停止了工作，而且也不可能安装新的插件。Mozilla 通过“研究”系统发布了一个快速修复更新。这需要选择“遥测”功能，这让大部分用户感到不满。5 月 8 日发布了一个不是基于“研究”的永久性修复程序，为桌面和 Android 用户提供了 66.0.5 Firefox 更新，为 Firefox ESR 提供了 60.6.3(扩展支持版本，为大学和企业等需要大规模设置和维护 Firefox 的大型组织开发)。

5 月 9 日，Mozilla 在官方博客上发表了一份名为“当事情出错时我们该做什么”的声明，对整个情况表示道歉。

> 我们努力让 Firefox 成为一个很好的体验。上周末我们失败了，我们很抱歉。

Mozilla CTO Eric Rescorla 也发表了一篇博客文章，介绍了宕机的技术细节，以及该团队如何提供解决方案来修复整个问题。他还列举了从整个灾难中吸取的教训，并承诺将尽快发布更详细的分析。

参考资料:

*   [https://hacks . Mozilla . org/2019/05/technical-details-on-the-recent-Firefox-add-on-outage/](https://hacks.mozilla.org/2019/05/technical-details-on-the-recent-firefox-add-on-outage/)
*   [https://blog . Mozilla . org/addons/2019/05/04/update-about-add-ons-in-Firefox/](https://blog.mozilla.org/addons/2019/05/04/update-regarding-add-ons-in-firefox/)
*   [https://blog . Mozilla . org/blog/2019/05/09/what-we-do-when-things-go-error/](https://blog.mozilla.org/blog/2019/05/09/what-we-do-when-things-go-wrong/)

### 微软宣布 Chromium Edge 浏览器的新功能

微软在他们的 Windows YouTube 频道上发布了一个 1 分钟的视频，从中可以一窥微软 Edge 的未来。The Verge 捡了个球，发表了一篇文章，详细解释了主要的变化是什么。

第一个特点是浏览器无缝内置的 IE 模式。这主要是针对拥有基于微软专有技术的内部 web 应用的企业和大型实体。企业还可以选择用公司标志定制`New Tab`页面，将一些网站的快捷方式加载到顶部标签中，并与微软搜索和 Office 365 集成。

第二个主要功能是允许用户创建收藏，他们可以与朋友或家人分享或导出到 Word 或 Excel 中。

最后但同样重要的是，将会有一整套与隐私相关的功能和选项，微软希望这能让 Edge 对用户更具吸引力。

谈到 Edge，微软在中断 15 年后重返 MacOS 浏览器领域。VentureBeat 表示，Edge 有两种版本可供 Mac 用户使用，即每日版本和每周版本。目前，无论是收藏还是隐私工具都不可用，预计今年晚些时候会有更新。

参考资料:

*   [https://www . the verge . com/2019/5/6/18527875/Microsoft-chromium-edge-new-features-build-2019](https://www.theverge.com/2019/5/6/18527875/microsoft-chromium-edge-new-features-build-2019)
*   [https://venturebeat . com/2019/05/07/Microsoft-edge-browser-arrives-for-MAC OS-in-2-beta-versions/](https://venturebeat.com/2019/05/07/microsofts-edge-browser-arrives-for-macos-in-2-beta-versions/)

### 2019 年 JavaScript 趋势一瞥

CV 编译器发表了一篇文章，强调了现有的 JavaScript 趋势。他们分析了今年 4 月发布的 300 份工作清单，其中 JavaScript 是主要要求的语言。

React 以 267 个列表稳坐榜首，其次是 **Angular** (195)和 **Node.js** (176)。开发人员还需要熟悉 **Git** 、**持续集成**或**测试驱动开发**。

文章还提到了图表中没有显示的技能，如**敏捷** (150)、**(101)或**可伸缩性** (70)。行业专家引用了一些话，总体结论是，现在是 JavaScript 开发人员的最佳时机。**

 **参考:

*   [https://cv compiler . com/blog/game-of-frameworks-JavaScript-trends-of-2019/](https://cvcompiler.com/blog/game-of-frameworks-javascript-trends-of-2019/)

### CodePen 刷新他们的工作板

CodePen 宣布了一个更新的工作公告板，包括视觉和引擎盖下。为了庆祝这一点，他们在整个五月都在进行宣传。新工作降价 100 美元，持续 60 天，是正常时间的两倍。

如果你(或你的公司)有一份网页开发的工作，并且想把它提升给来自世界各地的顶尖开发者，这是一个很好的选择。不要错过它。

参考资料:

*   [https://blog . code pen . io/2019/05/01/a-brand-new-job-board-100-off-job-posts-in-may/](https://blog.codepen.io/2019/05/01/a-brand-new-job-board-100-off-job-posts-in-may/)
*   [https://codepen.io/jobs](https://codepen.io/jobs)

### Web 项目指南书

2019 年已经为我们带来了两本很棒的前端开发者手册:[FreeCodeCamp 的 CSS 手册](https://medium.freecodecamp.org/the-css-handbook-a-handy-guide-to-css-for-developers-b56695917d11)和[前端大师的前端开发者手册](https://frontendmasters.com/books/front-end-handbook/2019/)。今天我向你展示一份新的——网络项目指南。这本书是由 Blend Interactive 的 Deane Barker 和 Corey Vilhauer 编写的，它将在编写时发布。前两章已经推出，每月还会推出两章新内容。

参考:

*   [https://www.webprojectbook.com/](https://www.webprojectbook.com/)

今天到此为止。我正在努力让这每周发生一次，并得到一个稳定的出版时间表。感谢您的阅读，希望您将来能再来。

* * ***