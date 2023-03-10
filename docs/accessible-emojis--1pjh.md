# 无障碍表情符号

> 原文：<https://dev.to/finallynero/accessible-emojis--1pjh>

我最近发表了一篇关于[构建可访问的表单](https://dev.to/finallynero/building-accessible-forms--46ke)的文章，这是一个续集，好吧，不是续集，但是如果你想构建可访问的应用程序，你可能需要这篇文章。

所以我今天在我的作品集网站上工作，我需要给一个段落添加表情符号，我想到了使用字体很棒的图标，但我不想使用它们，因为我要给我的网站添加一个额外的依赖，所以对我来说显而易见的选择是使用 Unicode，因为它有我需要的确切图标。

很多时候，我在 HTML 中使用 Unicode，我只是把它放在标记中，甚至没有考虑可访问性的问题。

[https://codepen.io/finallynero/embed/pYZmmv?height=600&default-tab=html,result&embed-version=2](https://codepen.io/finallynero/embed/pYZmmv?height=600&default-tab=html,result&embed-version=2)

我今天正要重复这一点，直到我忍住了，说这看起来不可访问，我做了一些研究，它实际上不是。

首先，表情符号是一个图像，没有任何东西表明它在上面的演示中。所以我们必须使用`ARIA: img role`属性来识别作为图像的元素。其次，使用`aria-label`属性给表情符号一个易访问的名称。

[https://codepen.io/finallynero/embed/EMpBdE?height=600&default-tab=html,result&embed-version=2](https://codepen.io/finallynero/embed/EMpBdE?height=600&default-tab=html,result&embed-version=2)

请记住，每个人都可以访问互联网及其内容，这一点很重要。