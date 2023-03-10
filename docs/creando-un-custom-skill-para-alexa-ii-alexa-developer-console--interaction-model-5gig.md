# 创建一个自定义的 Alexa 技能(二):Alexa 开发人员控制台和交互模型

> 原文：<https://dev.to/kini/creando-un-custom-skill-para-alexa-ii-alexa-developer-console--interaction-model-5gig>

正如我在[上一篇帖子](https://kinisoftware.com/creando-un-custom-skill-para-alexa-i/)中所评论的那样，一旦我有了该怎么办的想法(一种通过某个日期的电影明星来认识的技能)，我就用 [Alexa Developer Console](https://developer.amazon.com/alexa/console/ask) 从“顾客”部分开始了。顺便说一句，这个网站没有翻译成西班牙文，除了用英语之外别无选择。

## Alexa 开发者控制台

[![](img/42c92d79ea1f6895a37e43ab05315e1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZsRmKnGC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-18.00.05.png)

在这里，您可以创建自己的技能，并会列出您拥有的关于某些部分(如分析或 beta 管理)的信息和直接操作的所有人员。您有一对指向控制台文档的链接，包括视频链接和文本链接。他们对你了解网站很有用。一旦你发表了一项技能，你就会出现双重但状态不同的游戏，一个是开发游戏，另一个是亚马逊的 Alexa skills 商店。我会在另一篇文章中谈到这一点。

他要求我们创建我们的技能的第一件事是:名字、技能语言和技能类型。你可以在这里查询不同类型的。可用的类型取决于您选择的默认语言。嫁给我的技能想法的家伙是“T2【自定义技能】。

[![](img/be9a65a1b865f46d5a50281974dcd3c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lIo8VsL_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-18.10.22.png)

创建后，您将进入一个窗口，该窗口将允许您访问技能开发所需的所有内容。大部分时间你会花在控制台的“构建”部分。在这里，您将拥有构建您的技能所需的一切，设置和发展您的技能。

[![](img/31346e12d6d79b83cb56ef7564fb263e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6vt8V2ST--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-18.21.20.png)

按照“技能生成器核对清单”，您将完成准备好技能所需的最低要求。有两个基本部分:技能交互模型和用于处理技能请求的服务器端。在这篇文章里，我只谈第一件事。

## 交互模式

[技能交互模型](https://developer.amazon.com/docs/custom-skills/create-the-interaction-model-for-your-skill.html)至少由两部分组成:你技能的调用名称和必须符合顾客要求的意图集(动作)。

### 调用名称

[![](img/6e1dfcb0193380909c8732dfb46bb65e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qC3XeQ4_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-18.47.25.png)

调用名称将是用户开始与您的技能交互时使用的名称，并且必须满足若干要求。我认为所作的选择相当重要，因为用户可以调用技能并执行一次性的意图，即一次性调用。例如，“Alexa，问电影明星本周的电影”，一定有道理。用户也可以通过“Alexa，开电影首映式”来达到同样的效果，技能可以用某种方式回答，然后“告诉我本周的电影”。我很喜欢使用一次性调用的方式，在设计模型时想重点关注它。

### 意图

[![](img/e50c78ae46ea7089bb52c6de33438bfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DBpiwzqs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-19.02.15.png)

意图由两部分组成:要用来调用它的螺母(语句)和您可以拥有的可选插槽(参数)。关于食物的一些事情:

*   在同一意图中，不能有两个重复的螺母。你会很快意识到这一点，不用插槽，但当你使用插槽时，你最终可以创造出相同的螺母，它会跳过你。当我开始从不同的食物中提取插槽时，我遇到了 mi。控制台本身就证明了这一点。
*   Alexa 使用这些术语来推断用户想要做的事情的意图。您可以在控制台中以两种不同的意图重复相同的意图，并且在构建模型时不会收到错误消息。但在必须推断意图的时候，Alexa 会有麻烦，而且，我并没有读太多关于它的内容，行为并不是决定性的。无论如何，在认证过程中，这是其中一项验证，您必须纠正(true story:))

### 强制尝试

创建新技能时，有些强制性尝试已经由 Alexa 预先定义，您必须有封面，控制台会在“构建于尝试”下向您显示这些尝试。你会发现它们是基本的，没有插槽，我把它们解释如下:

*   *导航家*，用于多式联运技能，有屏幕的人，相当于退出
*   *取消*，将用于取消一些正在进行的交互
*   *停止*，停止使用技能
*   *帮助*，用户会调用它来请求我们的技能帮助

[![](img/760999aed9cd10dd47c38a0bc49d2bbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SOzR2R5x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-19.18.05.png)

你必须为每一个人选择他们要援引的食物，他没有了。完成服务器部分后，我们将看到如何将意图与其处理程序链接起来以响应用户。

### 自定义意向

这些尝试将真正定义我们希望在技能上涵盖的使用情形。就我而言，我只需要一个:要求在某一时刻放映电影。我先把它简化了假设它总是"本周"。这样，我的意图就不会有暂时的插槽，我会集中精力掌握一种功能性的技能，尽管不如我所希望的那样全面。

[![](img/63418cd9fa60e5f6ffd945930e6517ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wFoOCHaY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-20.23.11.png)

创建意图时，您可以选择使用 Alexa 库中的现有意图，如强制使用的内置意图或自定义意图。对我来说，没有一个已经存在的。

意图名称应能说明要复盖的相应操作。我叫他" newreleasesintent "是因为这是为了满足明星们的需求我跟踪了我在 builtin 看到的名字转换。

一旦创建，我们现在需要补充的只是要调用的螺母。

[![](img/35f084cad57259ac55f9218563a5f0c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cShXQibg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-20.29.44.png)

这是一种艺术，为意图创造灵感，而他们添加的越多，正确的识别就越好。[在这里](https://developer.amazon.com/docs/custom-skills/best-practices-for-sample-utterances-and-custom-slot-type-values.html)我按语言给你们放一本好做法指南，很有帮助。

### 建立模型

这样我们就有了第一个版本的模型。我们需要点击“构建模型”按钮，它会生成。我对此没有异议，我也不太清楚您是否在执行此步骤时进行验证，我想是的。医生可能有这方面的信息。

生成的模型只是一个 JSON，其中包含了我们使用控制台构建的所有信息。此 JSON 可以在生成和修改后查看，而不是使用螺母输入和我们之前看到的所有内容。

[![](img/886769a7be1f340e8ab0cc5876f6e96a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I0M4sDRP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kinisoftware.com/conteimg/2019/01/Screen-Shot-2019-01-19-at-20.35.43.png)

此文件可以下载，当我们进行服务器端项目时，可以添加并使用“[命令行工具”](https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html)部署我们完整技能的版本，而无需使用站点。我们将在另一篇文章中看到这个，但我是从我在这里所说的开始的。于是我熟悉了技能的概念和部分，然后开始使用命令行。

在下一篇文章中，我将介绍如何使用 AWS Lambda 制作服务器部件，并将它与我们的技能连接起来，以便对其运行情况进行第一次全面测试。

感谢你走到最后，因为它是一块好砖；）