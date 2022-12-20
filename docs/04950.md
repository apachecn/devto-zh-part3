# 边缘:发现(并修复)跨浏览器问题

> 原文：<https://dev.to/canderson93/on-the-edge-finding-and-fixing-cross-browser-issues-1g8c>

我们每个人都经历过。经过一番努力，我们终于完成了一个网站——不，是一件艺术品。一切运行完美——动画流畅，布局稳健，一切看起来都很完美...但是当你在 Edge 中打开它。

你的胃在下沉——看起来很糟糕。这不是你孜孜不倦建立的网站。一切都坏了！动画是错误的，布局到处都是，一切看起来很糟糕。

您已经成为跨浏览器兼容性的受害者。

尽管看起来你可能需要彻底改变一切，准备大规模重写，但事实证明调试跨浏览器 bug 比你想象的要容易。

跨浏览器的错误并不特别，会像其他错误一样表现出来，导致抛出错误或意外行为。因为它们发生在浏览器之间，所以它们*总是由以下两个原因之一引起的:*

1.  您正在使用浏览器中不存在的功能。
2.  您在浏览器中使用了*不同于*的功能。

## 发现漏洞

尽管有一个奇特的介绍，你所有的错误都跃然眼前，但很可能你的许多浏览器错误在浏览你的网站时并没有立即显现出来——所以你需要做一些测试。

在特定浏览器中发现错误的最好方法是*使用*那些浏览器。你应该在你打算支持的每个浏览器上查看你的网站，确保一切正常。如果你发现了一个 bug，你应该记下来，以后再来找它，或者你可以尝试马上深入研究它。

理想情况下，您应该在真实的设备上执行所有的测试，但是获取您可能需要的所有版本的设备是不现实的。相反，你应该在你所有的设备上进行测试，然后使用一个[模拟器](https://www.virtualbox.org/wiki/Downloads)或者一个类似[浏览器堆栈](https://www.browserstack.com/)的服务来检查剩下的部分。

### 应该测试哪些浏览器？

简单地说，测试你要支持什么。如果你没有任何其他限制(比如一个坚持使用 IE11 的客户)，一个好的基线是主流浏览器的 3 个最新版本。_ 这将在兼容性和所有最新的浏览器技术之间给你一个很好的折衷。

## 隔离 bug

太好了！你发现了一个 bug！下一步是找出它在哪里，出了什么问题。

任何跨浏览器的问题通常都可以追溯到一个不兼容的问题——这里你必须依靠标准的调试技术:`debugger`语句，或者使用浏览器的开发工具(他们都有这些工具——甚至 Internet Explorer 也有不错的调试工具)。

## 如何修复

在任何情况下，跨浏览器的错误都需要一些研究和实验。你应该检查像 [MDN](https://developer.mozilla.org/en-US/) 和[caniuse.com](https://caniuse.com/)这样的资源，以检查兼容性和已知的跨浏览器问题。

### 该功能不存在

遗憾的是，并非所有浏览器都支持所有功能。如果你使用的功能在你测试的浏览器中不存在，你仍然可以做一些事情。

如果问题出在 JavaScript 上，你最好的办法是尝试找到一个 polyfill——这是一段代码，它将取代不存在的功能。这并不是完美的(你不能填充关键字)，但是它可以让你无缝地修改 API 函数。

如果不能聚合填充，下一个方法是使用不同的、足够好的替代物来替代不支持的功能。

在 CSS 中，这是通过提供两个值来实现的。当 CSS 遇到一个它无法识别的值时，它会忽略它——或者如果它两次遇到相同的值，它会使用第二个值。这意味着我们可以提供后备值:

```
.grid {
    /* In browsers that support display: grid, this is overridden */
    display: block;

    /* In browsers that don't support display: grid, this is ignored */
    display: grid;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中，创建备份功能的最佳方法是使用*特性嗅探。*这是通过在尝试使用某个功能之前测试它是否存在来实现的。可以自己执行这些测试，但是最好使用像 [Modernizr](https://modernizr.com/) 这样的库。

```
if (Modernizr.webgl) {
    // this browser supports WebGL
    let ctx = canvas.getContext('webgl');
    draw3d(ctx);
} else {
    // we don't have WebGL support, so we perform a 2d drawing
    let ctx = canvas.getContext('2d');
    draw2d(ctx);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 功能不一致

在我看来，比功能不存在更烦人的是功能不一样。不幸的是，浏览器是由不同团队开发的不同项目，所以某些 API 中的不一致是不可避免的。

不一致的行为往往源于一些浏览器在接受什么方面更加灵活(Firefox 和 Chrome 通常非常灵活)，而一些浏览器则更加严格(比如 Safari 和 Internet Explorer)。

幸运的是，由于灵活的浏览器，你的解决方案通常是改变你的方法来匹配限制性浏览器的行为版本——灵活的浏览器如 Chrome 和 Firefox 通常也会处理这个版本。

我过去处理过的一个问题是，如果没有一个单元，Internet Explorer 不会在它的`flex-basis`属性中承认`0`。

```
.flex-container {
    flex-basis: 0; /* Breaks in IE */
    flex-basis: 0px; /* Works in all browsers */
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

跨浏览器问题与普通的错误没有太大的不同——事实上，它们通常是带有预先存在的如何修复它们的指南的错误！

一条重要的建议是一定要*寻找*这些跨浏览器的问题——它们完全有能力把你的项目从一台微调过的机器变成一堆闷烧的烂摊子——而你对此一无所知！

如果你对 JavaScript 和 Web 开发感兴趣，[注册我的时事通讯](https://pages.convertkit.com/9f030a68c9/cd303b3d7b)。