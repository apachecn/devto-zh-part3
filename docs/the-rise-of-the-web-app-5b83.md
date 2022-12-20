# 网络应用的兴起

> 原文：<https://dev.to/gtanyware/the-rise-of-the-web-app-5b83>

这篇文章讲述了一段始于 20 世纪 70 年代初的旅程，这段旅程没有行程，也没有可预见的终点。

## 旅程开始

第一批微型计算机由原始的微处理器芯片驱动，最初只能用数字编程，每种类型使用不同的数字。所以在 1975 年的摩托罗拉 6800 中，代码

```
86 0d 
```

Enter fullscreen mode Exit fullscreen mode

是一条将回车符放入累加器的指令，但是同样的代码对 Zilog Z-80 来说意味着完全不同的东西或者什么都不是。我们也有汇编语言，这是同样的东西，但为了人类的利益，数字被字母代码取代，同样的指令是

```
lda a 0x0d 
```

Enter fullscreen mode Exit fullscreen mode

在那些日子里，4 千字节是一个很大的内存量，所以程序必须短而有效。它们通常是用汇编语言编写的。高级语言编译器需要大量的内存，所以它们运行在更大的机器上，但是对于普通人来说，访问这些内存是困难和昂贵的。这并不重要，因为很少有人家里有微型计算机。

随着内存成本的下降，拥有更多内存变得切实可行，到 20 世纪 70 年代末，8 位计算机出现了 64k 字节的完整内存，足以运行一种高级语言。这真正标志着计算机革命的开始，因为在计算机上编写应用程序成为可能，大众市场软件由此诞生。

## 计算机革命

接下来的十年见证了家用电脑、IBM 个人电脑、麦金塔电脑以及我们现在都很熟悉的主要电脑软件的问世；文字处理器、电子表格和操作系统。但旅程的下一个主要停靠港是在 20 世纪 80 年代末，随着万维网的诞生。现在第一次有可能将家用电脑连接到互联网上，在此之前，互联网一直是大学、军队和工业的工具。

真正改变事情的不是网络本身，而是浏览器。因为这个应用程序本身什么也不做，而是依赖于互联网提供的内容。为了处理这些内容，浏览器有一个固定的程序，在你的眼前将 HTML 代码转换成网页。提供的唯一交互性是超链接，这导致向服务器发送新页面的请求，但是不久之后，浏览器代码被增强以在这一领域做更多的事情，并且在 20 世纪 90 年代中期，第一批浏览器编程语言以 VBScript 和 JavaScript 的形式出现。第二种被证明是更受欢迎的，从那以后 JavaScript 就成了浏览器编程的唯一选择。

近 20 年来，应用程序是你安装在电脑上的程序，浏览器是用来给你下载它们的工具。但随着互联网速度的提高，在浏览器中下载和运行应用程序变得切实可行。优势显而易见；无需安装或更新，只需运行即可。任何地方。

但是，这些应用程序中的大多数仍然是独立的；一旦加载，它们就和互联网本身没什么关系了。但是，随着互联网速度和可靠性的不断提高，浏览器应用程序可以开始在网上无缝运行，以最适合自己的方式进行分发。我们到达了网络应用程序。

现在，随着越来越多的应用程序由网络应用程序完成，我们看到已安装应用程序的缓慢下降。一些个人电脑，如 ChromeBooks，甚至不提供安装应用程序的方法；一切都通过浏览器完成。这个过程处于中途，所以我们看不到它的走向；我们只能猜测。但我的猜测是，最终几乎所有的事情都将通过浏览器来完成，甚至使操作系统变得多余，或者沦为浏览器的核心组件。

## 网络应用

那么，web 应用程序——以及一般的应用软件开发——将走向何方？目前，浏览器唯一能理解的语言是 JavaScript，这种语言以能够做你需要的一切而闻名，也以有六种不同的方式做任何一件事而闻名，并且以只要有一点点机会就能彻底搞砸而臭名昭著。

为了解决这个问题并带来一些有序的表象，大量的工具和框架被开发出来，从 JQuery 开始，然后是 Bootstrap、Angular、React、Vue 等等。大多数这些*将*添加到 JavaScript 中，而不是替换任何东西，尽管 JQuery 有点特殊，因为它提供了替代的、更简单的和更一致的方式来做常见的事情。在这方面，它更像是一种建立在 JavaScript 之上的稍微高级一点的语言。

框架还做的事情之一——JQuery 做得很好，其他的就不那么好——是给 JavaScript 的混乱带来一定程度的秩序。因为需要继续支持几十年前的网站，这种语言的每一个特征都还在。每次添加一个新特性，都会产生一个奇怪而奇妙的新语法，例如，胖箭头和三重等号。毫无疑问，这是可行的，但代价是这种语言变得更加难以学习和掌握。因此，有人开玩笑说“你不知道 JS”。没人知道。

当前设计新框架的热潮只是旅程中的一步。所有当前的框架都有一个共同的问题，即它们很复杂，很难学习，这意味着只有有经验的程序员才能正确使用它们。更糟糕的是，在被新的更时尚的东西超越之前，他们似乎只有有限的生命。(如果不是需要更新世界上的每一个浏览器来添加一种新的语言，JavaScript 本身就会发生这种情况。)另一方面，网站有很长的寿命，在许多情况下需要维护十年以上。很明显，找到熟练的程序员来维护一个五年前就变得不流行的框架既不容易也不便宜。

我不希望暗示框架是一件坏事，但是在它们当前的形式中，当涉及到表达客户感兴趣的有点随机的逻辑时，它们过于复杂，其中系统在一组任意的和频繁变化的业务规则中满足用户。每样东西都有它的位置，框架的优势在于构建稳定、自包含的组件，这些组件几乎不需要维护，并且通过定义良好的接口与系统的其余部分进行交互。由此出发，断言整个系统应该以同样的方式进行管理是错误的。这对脸书来说可能行得通，但我们很少有人会和一千个其他工程师一起工作，一起建设这种性质的网站，或者一起维护它们。

目前，我们看到性能稳定增长，而使用方式没有任何根本改变。很少有 web 应用程序真正测试 JavaScript 的性能。甚至对我来说仍然代表着魔法和巫术的谷歌地图也可以在任何智能手机上运行。我们有多余的表演，我们将看到旅程的另一个阶段；这一点我可以肯定。它会是什么样子，我们只能猜测。可能不止一件事。也许人工智能将帮助我们构建和维护应用程序，但这可能会在旅程的后期到来。

把性能的提高和复杂性的增加放在一起，我们就有机会了。有可能在 JavaScript 之上有另一层，去掉所有使用复杂、难以理解的底层代码的需要，代之以更简单的代码。我的意思并不是能力下降，而是能够通过每条指令做更多的事情。JQuery 是朝着正确方向迈出的一小步，但是如果你回顾这段历程，你会发现我们曾经走过这一步。

## 大得吓人

在 20 世纪 80 年代中期，革命性的 Macintosh 计算机的用户可以运行一个名为 HyperCard 的程序，这是一个平面数据库、图形系统和编程语言的组合，后者被称为 HyperTalk。在网络出现之前的几年，HyperCard/HyperTalk 在创建易于理解和维护的灵活用户界面方面表现出色。就像是早产了一样。很少或没有接受过计算机培训的用户可以建立复杂的“堆栈”，正如他们所称的那样——交互式图形数据库应用程序——来满足各种需求。就像网络应用一样，但是没有连接。

HyperTalk 被设计得尽可能像英语，所以你可以写

```
put the first character of the third word of line 5 of card field "sometext" into theChar 
```

Enter fullscreen mode Exit fullscreen mode

这不需要任何计算机培训就能理解。这个系统是如此的与众不同，以至于它赢得了“极其伟大”的绰号；在此后的 30 多年里，我还没有听说过这样的赞美适用于其他任何事情。

当被问及某件事情应该如何工作时，像这样的代码与我们所写的很接近。领域专家和程序员都可以阅读它，我还没有遇到过一个网页的外观和行为不能用这种语言描述的。像 Google Maps 这样的复杂功能块可以作为打包的组件包含在内，带有自己简单的操作关键字，让所有人都可以理解页面的外观和行为，以及“为什么要用其他方式呢？”悬在空中。

JavaScript 可能是浏览器唯一理解的语言，但它现在已经强大到足以充当汇编语言，我们可以用它来编写行为类似本机代码的高级语言。这确实是我们打破不断增加复杂性的无休止循环并实现更高生产率的唯一方法。

我使用 HyperTalk 的时间给我留下了不可磨灭的印象，20 多年来我一直在编写试图模仿它的脚本语言，去年我用 JavaScript 编写了我的第一个脚本语言。从那以后，我一直用它来建立网站，同时试图说服其他人考虑采用这项技术来处理他们自己页面的业务逻辑。我将很快发表一系列关于如何使用它来构建 Web 应用程序的文章。

谢谢你陪着我。如果你有兴趣知道一种高级浏览器脚本语言是什么样子的，请前往 [EasyCoder](https://easycoder.github.io) 。

弗洛里安·奥利佛在 [Unsplash](https://unsplash.com/search/photos/language?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的标题照片