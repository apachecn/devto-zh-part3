# 开源项目中完全自动化发布的方法

> 原文：<https://dev.to/kreuzerk/the-way-to-fully-automated-releases-in-open-source-projects-4057>

#### 如何结合 GitHub 和 Travis 使用约定提交和语义发布。

[![](img/d2fa2d8faf2658580b7fba01b4bc595c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1EwGKBfd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2600/1%2A6u8pu-lMmd3maevO9Whzzw.png)

开发和测试只是创建软件艺术的一部分。另一部分是运送艺术品。🚢

交付软件需要一些步骤；我们需要运行测试，构建我们的工件，并在某个地方发布或部署它。大多数情况下，这些步骤是在一个管道中执行的。

管道是自动化所有必需步骤的一种方式。因此，需要具有必要配置的工具和 CI 服务器。

许多公司有复杂的设置，需要整个团队来维护这样一个平台。

哇！😮听起来很复杂。“也许最好放弃 CI 和管道。我手动做所有事情”可能是你现在的想法。

但是没有！感谢现有工具的巨大优势，它并不复杂！

了解如何创建一个管道来自动化您的发布！