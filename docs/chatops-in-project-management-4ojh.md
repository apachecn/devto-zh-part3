# 项目管理中的聊天工具

> 原文：<https://dev.to/riterapp/chatops-in-project-management-4ojh>

几年前，Jesse Newland 展示了“Github 上的 ChatOps”，这是一个开源聊天机器人 Hubot，用于自动化部署、绘图、监控、供应和许多其他事情，以增强远程工作的文化。这是 ChatOps 历史的开端，一种通过聊天管理运营的新方式。如今，它不仅仅是开发运维与部署的问题。项目经理和任何在软件开发团队中工作的人也可以使用它，而且他们中的许多人已经这样做了。

### 什么是 ChatOps？

ChatOps 是一个“协作模型，有助于将人员、流程、工具和自动化连接到一个透明的工作流中”。不是在后台做一些工作，而是将所有任务的执行转移到一个普通的聊天室，让团队中的每个人都可以看到它们。

当你使用你常用的聊天应用程序时，你输入用户友好的命令，聊天机器人就会匹配某些脚本和插件。除了聊天机器人，还有服务器端应用程序监听这些命令并执行任务。在您键入必要的命令后，某些操作将在没有您进一步参与的情况下执行。这些可以包括相当广泛的任务，例如部署应用程序、配置、备份、通知、日志记录、运行测试等。事实上，你可以用 ChatOps 写任何你能想到的东西。

ChatOps 实现了一种对话驱动的协作，这其实并不新鲜。然而，ChatOps 将它带到了一个新的水平，将最古老的协作方式与最新的技术结合起来。结果，ChatOps 变成了应用于商业、技术、科学等不同领域的强大工具。

[![im1](img/37fb9de5f5397af25312941f3e3264d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--74SEgiF3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vault8.io/88e24e3e827045329f0066ecb139.png/autoorient%2Cresize_fit-1920-1080/chatops.png%3Fp%3D0aa930d138b25b338ee16fe%26s%3D19dfa351da7b8c17b407cef5ccdf8fff05811f40)

### 项目经理聊天平台

项目管理和软件开发也不例外。使用 ChatOps，您可以将您的聊天应用程序与任何第三方软件集成，如您的项目管理工具、时间跟踪系统、计费应用程序、不同的协作软件等。你的队友将能够直接在他们的聊天室中执行所有命令，而不是在众多应用程序之间切换，而相应的机器人将完成剩余的工作。

今天，高级软件开发团队已经在实践中使用这种或类似的解决方案来推动聊天室中的组织活动，并避免不断的会议和只会分散员工工作注意力的额外问题。一些例子可以是 HipChat、Flowdock、Campfire 和 Slack 附加组件。Slack 提供了自己的内置 Slackbot，但也有许多第三方解决方案。例如，StackStorm、Deploybot 和 Blockspring 机器人可以与 Atlassian 的 HipChat 或 IRC 等其他聊天应用集成。例如，聊天机器人可以用来创建新的票证，添加里程碑，关闭任务，提交花费在它们上面的时间，将文件附加到任务等等。

事实上，[聊天机器人的用例](https://riter.co/blog/why-it-s-necessary-for-today-s-business-to-use-chatbots)只会受到你的想象力和团队需求的限制。有技术背景的团队通常会尝试使用高级机器人来自动化尽可能多的日常任务。你手动做的大多数重复性工作都可以由机器人来完成。因此，我们可以找到许多开源的高级定制机器人，与第三方软件的深度集成，试图使用人工智能和人工智能来提升机器人的能力。非技术团队通常使用现有的聊天机器人，这些机器人已经实现了最常见的任务。

### 为什么要用它

ChatOps 为您提供了另一种协作方式，具有明显的优势:

*   **“边做边交流”。**不再有类似“您创建票证还是我创建票证？”每个人都知道工作的哪一部分已经完成，哪一部分还应该完成。所有的行为都在普通的聊天室里进行，因此大多数问题都变得无关紧要。由于对工作流程的普遍了解和透明度，会议的数量也可以减少。

[![im2](img/d9e1c0fbc6ea683cd2b4affae7be79cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Do0v7Ydy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vault8.io/2a2c6871aad9484fa5fcacf1cfb9.png/autoorient%2Cresize_fit-1920-1080/convo.png%3Fp%3D0aa930d138b25b338ee16fe%26s%3Debc54c11163a9e190f17ddf83707afc280d8f421)

*   **“寓教于乐，寓教于乐”。** ChatOps 可以加快新团队成员学习常见实践的速度，强化反馈循环、协作，并改善知识共享。

*   常规任务的自动化。过去需要手动完成的任务(通常伴随着人为错误)现在可以通过聊天机器人实现自动化。此外，聊天室中的所有命令对每个人都是可见的，这使得其他人在将来更容易找到和复制它们。

*   **即时协作。**我们通常不喜欢在处理任务时打开许多应用程序，而聊天应用程序大部分时间都是打开的。因此，当有人使用聊天机器人执行任务时，所有的更新都会立即被所有的队友看到。有了 ChatOps，我们可以将工具带入对话，进行实时协作。发现错误、及时建议帮助、响应变更和请求、减少花费在项目管理和合作上的时间变得更加容易。更不用说对远程工作团队的好处了。

*   **历史和审计。**使用 ChatOps，您可以获得所有团队成员执行的所有命令的完整历史记录，即使您的项目管理工具和其他使用的软件无法跟踪变更的历史记录。除了更高的可见性和透明度，您还可以获得项目的最新状态，并更清楚地了解最近队友的活动。

*   **工作人性化。通常，聊天机器人支持用户友好的命令和自然语言处理。有人可能会抱怨需要记忆命令，然而，这样的问题现在已经成为过去。今天，ChatOps 提供了用户特定的直观命令，因此人们可以向聊天机器人发送命令，就像他们向任何队友发送命令一样。此外，许多现代机器人提供的用不同的语言表达相同想法的能力，有助于消除沟通问题，使聊天机器人更具互动性。因此，今天的 ChatOps 允许以更人性化的方式完成工作。**

*   **更便宜更快。**当您仍在使用所有喜爱的工具时，您可以减少在上下文切换上花费的时间，因为大多数任务都可以在聊天室中完成。这不是什么秘密[有多少时间](https://riter.co/blog/some-disappointing-statistics-on-your-software-development-team-performance)浪费在管理工作区和其他行政事务上。

[![im3](img/dfc7dec925b083797582f23272341e89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jai8Gk4z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vault8.io/81e7bdb4a183475ca8bd13ae1577.png/autoorient%2Cresize_fit-1920-1080/bots.png%3Fp%3D0aa930d138b25b338ee16fe%26s%3D0f5d5a9b9659ee5c7744b5c3f734516bd6a282b2)

### 从哪里开始？

因此，ChatOps 是一种很好的方式来自动化任务，鼓励团队变得透明，相互学习，更快更便宜地工作，从一个地方保存和管理一切。

根据[最近的研究](https://www.pmi.org/-/media/pmi/documents/public/pdf/learning/thought-leadership/pulse/pulse-of-the-profession-2018.pdf)，在最近的几年中，项目管理软件的市场将会发生很大的变化，公司中的过程管理也是如此。项目管理的主要趋势将是更多的远程团队，人工智能的巨大影响，工作场所的最大自动化和各种活动领域的大规模集成。聊天室在工作流程中的作用将继续增加，同时对任务规划的结构化方式的需求将继续存在。

大多数从事项目管理工具的公司现在已经开始考虑这些变化了。例如，在 [Riter](https://riter.co/blog/future-of-project-management-riter-development-strategy) 中，我们关注它在连接插件的帮助下与第三方服务的广泛集成。全功能的 GraphQL API 和 Webhook 支持允许实现任何复杂性的聊天机器人，并自动化任何特定的任务。随着时间的推移，任何有自尊的项目管理工具都会为你提供各种各样的插件，甚至会有更多的人参与编写定制的开源机器人。

已经有足够强大的 ChatOps 工具可用于项目管理和相关需求。此外，你肯定需要花很多时间来找出什么最适合你的公司。只需在你的团队聊天室中尝试各种机器人和插件，选择最喜欢的。与同事分享您的经验，跟踪更新，因为许多优秀的解决方案正在出现。或者让你的软件开发团队为你的工作流程编写你自己的机器人。

更多相关信息:

[更多聊天工具示例](https://github.com/exAspArk/awesome-chatops)
[更多 Slack bots](https://www.producthunt.com/e/slackbots-and-integrations)
[更多关于项目管理中的聊天工具](https://chatbotslife.com/tagged/project-management)
[人工智能在当今项目管理中的应用](https://chatbotslife.com/artificial-intelligence-in-todays-project-management-a4de3647dcb4)
[为什么当今企业有必要使用聊天工具](https://chatbotslife.com/why-its-necessary-for-today-s-business-to-use-chatbots-33dd5a4919cf)

*最初发表于[Riter.co](https://riter.co/blog/chatops-inproject-management)。*