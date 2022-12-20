# 利用反应误差边界来改善 UX

> 原文：<https://dev.to/chingu/exploit-react-error-boundaries-to-improve-ux-hdk>

#### 如何自动化错误检测&报告

<figure>[![](img/545a79f6ad09eac3d28df122ef994d75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yCTknyil--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzvJpnhdbkKSjqYdFKmSDCg.jpeg) 

<figcaption>来自 Pexels.com</figcaption>

</figure>

[用户体验](https://www.interaction-design.org/literature/topics/ux-design) (UX)是一个广泛而复杂的学科，被开发社区中的大多数人认为是应用程序成功不可或缺的因素。然而，UX 在大多数应用程序中没有得到应有的关注的一个方面是围绕着[错误检测和报告](http://architectingusability.com/2012/06/05/designing-error-handling-for-maximum-usability-in-your-application/)。

太多的应用程序采用一种随意的方法来检测、处理和报告错误，因为开发人员认为这要么是苦差事，要么是不重要的。错的不仅仅是开发商。通常情况下，[产品经理](https://en.wikipedia.org/wiki/Product_manager)会将新功能放在错误处理和恢复等被视为平凡的项目之上。

然而，有令人信服的理由不仅要关注如何检测和处理应用程序故障，还要关注控制它们如何影响用户的 UX 考虑事项。

1.  用户希望应用程序能够工作，而不是失败。一般来说，用户倾向于记住失败及其影响，而不是他们从应用程序中获得的成功。如果你的用户必须遵循像[这个](https://docs.acquia.com/support/tickets/)这样的程序来报告每一个问题，他们会记得什么？
2.  [最佳应用](https://savvyapps.com/blog/what-makes-a-great-app)是那些对用户不可见的应用。目标是不妨碍完成工作，而不是必须处理使用应用程序的机制。
3.  [“感知即现实](https://www.elitedaily.com/life/motivation/perception-is-reality)”用户如何看待一个应用程序取决于信息、事件和结果的框架。有时候，当涉及到错误消息时，开发人员是他们自己最大的敌人。例如，“发生了灾难性错误！开一张票…”而不是“我们现在无法处理您的请求。有关更多信息，请参见……”。
4.  软件架构与支持特定业务流程的任何应用程序功能一样，都是一种特性。任何支持应用程序为用户增加价值的能力都应该被认为是一个特性。

有许多技术和工具可用于使错误检测和处理对用户更加透明，并提高开发人员响应的速度和质量。这些包括:

*   设计和实现一个全面的错误检测和处理策略
*   精心制作易于理解且可操作的信息
*   记录相关的应用程序数据和用户活动
*   自动化恢复
*   出于诊断目的自动截图
*   遇到错误时自动创建问题票证

本文的剩余部分将关注其中的两个——实现应用程序范围的错误检测策略和自动创建问题单。我们将使用 GitHub 上的陨石浏览器 repo 中的[功能/06-错误处理](https://github.com/jdmedlock/meteorite/tree/feature/06-error-handling)和[功能/07-创建-发布](https://github.com/jdmedlock/meteorite/tree/feature/07-create-issue)分支来演示这些。

### 实施错误检测&处理策略

<figure>[![](img/28d0595a24d863db66c70b92afb79213.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kNdctMM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhEbJvltnslRrdEzjWQ7Img.jpeg) 

<figcaption>照片由[卢卡布拉沃](https://unsplash.com/photos/XJXWbfSo2f0?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

实现错误处理策略似乎很简单。我们首先将最高级别的 React 组件封装在一个`try...catch`语句中，以拦截任何错误。