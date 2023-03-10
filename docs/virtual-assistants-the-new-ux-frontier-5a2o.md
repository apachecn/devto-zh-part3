# 虚拟助手:新 UX 前沿

> 原文：<https://dev.to/philipid3s/virtual-assistants-the-new-ux-frontier-5a2o>

最近，我围绕聊天机器人、数据分析和人工智能开发了一个 PoC。潜在的想法是探索消费数据和与后端服务器交互的新方法。

架构非常简单——在集中式业务环境之上，我构建了一个微服务(Nodejs/Express ),这是一个后端服务器，负责聚合各种数据源:SQL server、Blob 存储、xls 文件、外部 API。数据一旦被确认，将通过 Rest API 传递给下游服务。

另一个后端服务将致力于数据的分析和图形的生成。这项工作的自然选择是 Python (Flask)，因为专用于这些任务的库的多样性和质量:Pandas、Matplotlib 甚至用于机器学习的 Tensorflow。

建筑的原型:
[![](img/9b428ad6db18781d07346d2f65cf9996.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xBxejt7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j82n6p6rfkqydxz6m4o9.png)

最后——机器人——我们的用户界面。我在这个原型中使用 Telegram Bot 框架有两个主要原因:免费且易于实现。但是之后我肯定会尝试新的微软机器人框架以及 QnA Maker 和认知服务

当用户要求报告、信息或任何需要数据分析或机器学习的 KPI 时，流程如下:

[![](img/4fb8649b894b2e2fc695ac1fbe061578.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HaSqbNUQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oa5bedbc1gkgndy486bc.png)

作为结论，我只想说，机器人或虚拟助手提供了一种新的信息消费方式——它们将成为过去几十年来开发的所有重型客户端的现实替代品:在我们的意图和机器执行的命令之间没有障碍或复杂的界面。