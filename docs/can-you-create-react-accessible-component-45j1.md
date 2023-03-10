# 你能创建一个可访问的 React 组件吗？

> 原文：<https://dev.to/stereobooster/can-you-create-react-accessible-component-45j1>

让我们尝试一个有趣的实验。在[第一篇](https://dev.to/stereobooster/accessible-react-accordion-component-4p99)中，我们讨论了如何创建 React 可访问组件，在[第二篇](https://dev.to/stereobooster/testing-accessibility-with-cypress-2dfo)中讨论了如何测试它，在第三篇中我建议你尝试自己编写可访问组件。(严格来说不一定要 React，不然我就没法复习了)

我们将尝试编写可访问的选项卡组件。还有 [WAI-ARIA 创作实践 1.1。建议](https://www.w3.org/TR/wai-aria-practices/examples/tabs/tabs-1/tabs.html)。

根据建议，我用 Cypress 测试创建了一个启动库。我在这些测试中尽了最大努力，但是它们可能需要一些调整——用 e2e 测试很难做到测试优先的方法，它只在单元测试中顺利工作。特拉维斯·CI 会进行测试。

Tabs 组件与我们在第一篇文章中谈到的 accordion 组件非常相似，所以您可以使用第一个例子作为灵感。这可能是一次很好的学习经历。在我为第一篇文章创建和修复代码中的错误时，我学到了一些东西。