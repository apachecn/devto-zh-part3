# 将 uxJS(一个简化的 MVVM 框架)引入现代标准。

> 原文：<https://dev.to/ua1labs/bringing-uxjs-a-simplified-mvvm-framework-to-a-modern-standard-4gmd>

在我写这篇文章的时候，uxJS([https://github.com/ua1-labs/uxjs](https://github.com/ua1-labs/uxjs))在大约 5 年前作为另一个框架([https://github.com/joshualjohnson/jquery.x](https://github.com/joshualjohnson/jquery.x))发布，还没有作为 1.0 生产就绪库发布。uxJS 是 jQuery.x 的一个硬分支，试图提供一个 MVVM 框架的简化版本，而不使用 jQuery 作为提供该功能的主要力量。这个框架已经在生产中用于创建像 https://www.thetvshield.com/the-tv-shield-product-selector/[这样的用户体验。](https://www.thetvshield.com/the-tv-shield-product-selector/)

随着我们在 JS 领域的快速发展，我注意到又到了更新 uxJS 概念的时候了，并为 1.0 发布做准备。在目前的版本中，你可以在这里阅读所有关于这个概念的内容[https://github.com/ua1-labs/uxjs/blob/master/README.md](https://github.com/ua1-labs/uxjs/blob/master/README.md)。

以下是我认为我们需要做的事情，以使框架现代化:

1.  一起远离控制器。
2.  保留插件的概念，但是重构它，这样你就可以称它为“ux”而不是插件。
3.  删除 data-ux-plugins = " plugin 1 plugin 2 "并重用 construct data-ux="ux1 ux2 ",以便可以将多个" UX "定义分层到一个 DOM 节点。
4.  为了注册新的用户体验定义，api 应该从`ux.plugin()`重构到`ux.define(name)`
5.  更新整个库以使用 TypeScript
6.  创建一个可扩展的抽象 ux 类，用于注册“UX”定义。

这是我此刻正在思考的一些事情...

我知道，我已经听你说过了，“为什么还要另一个 JS 框架？”答案很简单...因为我们人类有能力推动软件向前发展。在过去的 6 年里，这个框架一直伴随着我，并将随着时间的推移继续发展。在它的剩余生命周期中，它仍将是 uxJS，但是当我们更新它的功能时，它将会不断更新。

我想我只有这些了！你想加入我的重构努力吗？