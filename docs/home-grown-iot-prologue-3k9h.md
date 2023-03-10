# 国产物联网-序幕

> 原文：<https://dev.to/azure/home-grown-iot-prologue-3k9h>

我总是有点像硬件修补匠。在我成长的过程中，我父亲会从工作单位把旧收音机、电话和其他电子产品带回家，连同螺丝刀和万用表一起递给我，让我摆弄它们。我有一个电子工具包，里面有各种各样的传感器、灯和开关，你可以把它们连接在一起，做出任何你想做的东西。我们有试验板、电线、烙铁、电阻器、电容器、发光二极管、开关和中间的所有东西来制造随机的小电子元件。

当我们有了第一台电脑，一台 486 66 DX(带涡轮按钮！)，我把它拆开，严格按照说明 a)知道电缆去了哪里，b)没有烙铁！帮助建立了我们家的令牌环网络，并最终为我们的房子装上了以太网。

因此，当廉价的消费级物联网在 Raspberry Pi 和 Arduino 上流行起来时，我自然会自己拿一些来玩。

我做了每个人用物联网设备都会做的事情…

[![Getting an Arduino LED to Blink; And Then Losing Interest; Looks like it's back to pixels for you!](img/3542787b36c80d42d0e2d2a8c9b0ce1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3otPXkUr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/01-001.png)

<cite>[来源@ThePracticalDev](https://github.com/thepracticaldev/orly-full-res/blob/master/gettinganarduinoledtoblink-big.png)</cite>

说真的，我有一个圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率圆周率类似地，我有一堆 NodeMCU 芯片，它们有 4MB 内存、16 个引脚和 wifi，只是放在架子上积灰。

当谈到物联网项目时，我的问题是我不知道该做什么，这是成功的一半。

## 突发奇想

2018 年底，我和妻子决定在我们的屋顶上安装太阳能电池板。我们总共有 18 块电池板，峰值输出为 5.5 千瓦时，足以满足我们家庭的能源需求。我们还得到了一个逆变器，一个 [ABB UNO-DM 系列](https://new.abb.com/power-converters-inverters/solar/string/single-phase/uno-dm-3-3kw-4-0kw-4-6kw-5-0kw-tl-plus)逆变器，将电池板连接到我们的电源，并将产生的多余电力送回电网。

最近关于逆变器的有趣的事情是，几乎所有的逆变器都带有内置的 wifi 端点，当然我们的也有。我把它连接到 wifi 上，看到了一些仪表盘。

[![System Overview](img/bb86c4e1c78b4a717caf94630516151a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GgyJS_sv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/01-002.png)

[![Generation Summary](img/3450c281025165a73009a9c21b0a7913.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u63ZRDIk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/01-003.png)

嗯，那很漂亮，不是吗？

但说到底我是一个开发人员，那么接下来我该做什么？当然是启动开发工具！

[![Finding network requests](img/948c994090d84b0e197821051faf1a3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ygxC4oCm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/01-004.png)

我发现 dashboard 只是一个 AngularJS 应用程序，它覆盖了一系列 HTTP 端点，并通过基本身份验证进行保护。更重要的是，数据只是基本的 JSON 有效载荷！

[![Example network respose](img/d3474caa58ca0d6fd24608aad0ad54d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JBSopSib--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/01-005.png)

## 我自己开发的物联网项目

现在，我的家庭网络上有一些东西正在生成有趣的数据，而且它正在运行一个 web 服务器，我可以使用一个认证模型连接到该服务器，我可以毫不费力地实现它。

这给了我一些目标。我的项目目标如下:

*   创建一个可以在 Raspberry Pi 上运行的解决方案，从我的逆变器中提取数据
*   将数据存储在某个地方
*   创建我自己的仪表板来查看产生的电力
*   看看数据中还有什么有趣的东西

由于这将是首次涉足真正的物联网项目，我想*把它做好*。我想要一个简单的本地开发体验，包括能够在我不在家的时候进行开发；我希望它易于部署；我想避免将我的 Pi(或逆变器)暴露在公共互联网上。

所以在过去的几个月里，我已经慢慢地解决了这个问题，并最终部署了我的太阳能电池板监控系统 [Sunshine](https://github.com/aaronpowell/sunshine) ！如果你想了解一下，代码在 GitHub 上，但是它是围绕着*我的*设置设计的，所以它不是一个真正通用的解决方案。其核心是一个运行在 Docker 上的. NET 核心应用程序(用 F#编写)，使用 [Azure 物联网中心](https://docs.microsoft.com/en-us/azure/iot-hub/?WT.mc_id=devto-blog-aapowell)进行设备连接，使用 [Azure 物联网边缘](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-aapowell)部署 [Azure 管道](https://azure.microsoft.com/en-us/services/devops/pipelines/?WT.mc_id=devto-blog-aapowell)，使用 [Azure 功能](https://azure.microsoft.com/en-us/services/functions/?WT.mc_id=devto-blog-aapowell)进行数据处理。

在这个系列中，我将讲述我是如何构建这个项目的，我使用的技术，我做出的决定以及为什么做出这些决定。我学到了很多建设这个(我已经大修了几次🤣)并希望它能为您的物联网项目提供一些指导。