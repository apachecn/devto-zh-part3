# 滑雪模拟器、Qlik 核心和实时分析——一个 Qonnections 故事

> 原文：<https://dev.to/qlikbranch/ski-simulators-qlik-core-and-real-time-analytics-a-qonnections-story-2ld1>

### 滑雪模拟器、Qlik 核心和实时分析——一个 Qonnections 故事

Qlik Core，React 和一大堆开源。了解我开发一个很棒的应用程序来搭配一些很酷的硬件的乐趣。

[![](img/89406b06e770f9ea5c5e6a13b5552f4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_39VyV-D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALfWIDmRMp3N_FBmWW4TZSg.jpeg) 

<figcaption>自己在超级好玩的 SkyTechSport 滑雪模拟器上</figcaption>

另一个连接来了又走了，今年我成为了一些真正有趣的事情的一部分。我们会议的主题发言人是林赛·沃恩，美国高山滑雪运动员，拥有 3 枚奥运会奖牌和 7 枚世界杯奖牌。因此，Qlik 想做一些真正酷的事情，我让 Qlik 负责技术产品营销的高级经理 Adam Mayer 联系我，让我领导这个令人兴奋的项目的开发部分。

[![](img/ec986f2c240ce0ed1e960cf4e2da51ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GFW_uIfB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiBSsTcq2enqv3VLMhU8YWQ.jpeg) 

<figcaption>自己和林赛·沃恩势不两立</figcaption>

为了完成这项工作，Qlik 与 SkyTechSport 合作，这是一家制造杀手级设备的公司，帮助运动员在比赛中保持领先。计划很简单:SkyTechSport 将提供超级酷的[滑雪模拟器](http://www.skytechsport.com/alpine-simulator)，供我们的与会者和维护它的人使用，在他们那边做一点开发，让我们访问模拟器生成的数据点，我们将构建一些令人敬畏的数据可视化来绕过它。我们的实现将包括游戏中的实时仪表板以及游戏后排行榜，以跟踪谁是榜首。所有这些都将包括一项慈善活动，Qlik 将为每一个成功通过的门向特奥会捐赠 1 美元。我负责实时应用程序，神奇的[阿图罗·穆尼奥斯](https://twitter.com/arturoQV)负责排行榜。未来肯定会有很大的发展，但是挑战马上就出现了。

项目源代码:[https://github.com/Qlik-Branch/qonnections-ski-simulator](https://github.com/Qlik-Branch/qonnections-ski-simulator)

需要处理的第一个挑战是模拟器如何传递数据。模拟器是一个快速的设备，其背后的软件是为视觉和物理反馈而构建的，所以所有的数据都在毫秒内发生。准确地说是 30 毫秒。因此，模拟器每 30 毫秒将数据保存到一个文件中。通过网络。不仅仅是保存数据，还要覆盖数据。这带来了两个问题。