# Web 应用程序的专用开发工具

> 原文：<https://dev.to/hamsterasesino/dev-only-tooling-for-web-applications-2ga6>

大家好，

在工作中，我正在开发一个非常复杂的 web 应用程序，该应用程序向用户呈现一个流，该流包含一系列收集用户数据的页面。如果用户想要到达一个页面，她必须通过所有的流程才能到达。有时这可能意味着多达 10 个页面，页面之间的后端可以节省 4-10 秒的时间。

我们，开发人员，过去每次想要对这些页面中的一个进行更改时，都必须经历相同的过程。所以可以想象多次重访流量上的一个页面的痛苦。

我想出了一个工具，通过复制`sessionStorage`中的用户数据，并重新创建流程到您离开的那一步，可以让您就地刷新页面。这几乎是瞬间发生的，节省了我们几个小时的时间。

我的问题是这个工具很难安装，这使得其他开发者不愿意使用它。我有一个单独的分支，但它需要不断修改文件，导致每次我们想将它与实际代码合并时都会发生合并冲突。

我考虑过使用 webpack 和一个基于环境在编译时替换的变量，所以我们可以只在本地使用它。问题是，即使被停用，代码也需要和产品代码一起存在。

我努力寻找一种更好的方法来使它更容易维护和使用。有人强烈反对将只开发代码与生产代码合并吗？什么会出错？除了基于分支的方法，还有其他选择吗？

谢谢你。