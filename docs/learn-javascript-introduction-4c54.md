# 学习 Javascript:简介

> 原文：<https://dev.to/alioukahere/learn-javascript-introduction-4c54>

那么，你是编程新手，还是只是想学习 Javascript 来将其添加到你的其他技能中？欢迎光临！这一系列教程是给你的，你只需要做一件事，练习我们将一起看到的东西。我永远不会停止说，不实践看教程是没有用的，真正学会编程的唯一方法就是实践。

在本教程中，我们将了解什么是 Javascript，它的不同版本，并了解 Javascript 代码的基础。让我们现在开始。

> 这篇教程是我写的关于 Javascript 编程的一系列教程中的第一篇，请确保不要错过接下来的任何教程。

Javascript 是最流行的编程语言之一，它现在被用于几乎所有与编程相关的领域:Web、移动、桌面软件、嵌入式系统、机器学习、视频游戏等等

Javascript 现在用于创建全栈 web 应用程序(前端和后端)。最近几年 Node.js 的兴起开启了 Javascript 在后端的使用，这属于 Java、Python、PHP、Ruby 等语言的领域……但是 Javascript 是什么呢？

## Javascript，它是什么？

Javascript 创建于 20 年前，是第一种也是唯一一种受 web 浏览器支持的脚本语言。它主要用于在 DHTML 页面上制作动画。

如今，正如我们在介绍中看到的那样，JavaScript 不仅在前端(在浏览器上)而且在后端(在服务器上)都得到了发展和执行，所以最初作为运行在浏览器中的脚本的简单语言已经成为几乎在任何地方都使用的全球语言。Javascript 将在任何包含所谓 Javascript 引擎的硬件上运行，有几个包括谷歌 Chrome 和 Opera 的 V8，Firefox 的 SpiderMonkey，Safari 的 SquirrelFish，…这些引擎读取并执行 Javascript。

用几点来定义 Javascript，我们会说 Javascript 是:

*   一种高级语言:它不提供对内存或 CPU 的低级访问，因为它最初是为不需要它的浏览器创建的。
*   **动态语言**:动态语言在执行静态语言在编译时执行的许多任务时执行。这有优点也有缺点，并且给了我们强大的特性，比如动态类型、后期绑定、反射、函数式编程、改变对象的执行等等。
*   **动态类型语言**:在 Javascript 中，变量不一定有预定义的类型。所以我们可以在程序执行过程中改变变量的类型。
*   弱类型语言:与强类型相反，弱类型语言不强加对象的类型，这允许更大的灵活性，但是拒绝用户安全性和类型检查(这是 TypeScript 和 Flow 旨在改进的)
*   解释型语言(interpreted language):它通常被称为解释型语言，这意味着它不需要编译就可以执行程序，不像 C 或 Java。实际上，出于性能原因，浏览器会在执行 Javascript 之前对其进行编译，但这对您来说是透明的:不需要额外的步骤。
*   多范例语言(multi-paradigm language):这种语言不应用特定的编程范例，不像 Java，例如，它强制使用面向对象编程，也不像 C，它强制使用命令式编程。您可以使用面向对象的范例，使用原型和新的类语法(来自 ES6)来编写 Javascript。你可以用函数式编程风格写 Javascript，用它的第一个类函数，甚至用命令式风格(像 C)。

先做一点设置，Javascript 和 Java 无关。Java 是 Sun 公司的一种编程语言，Javascript 是由 Brendan Eich 开发的一种语言。

对于这个小故事来说，JavaScript 的第一个版本叫做 LiveScript，但是当时 Java 已经存在并且已经非常流行，JavaScript 维护者认为将他们的语言定位为 Java 的小兄弟可以帮助很好地定位该语言，所以他们称之为 Javascript。

但是今天这一切都变了，Javascript 有了它自己的规范，叫做 ECMAScript，我们前面会看到。

## Javascript 版本

现在来说说 ECMAScript，那个诡异的名字。ECMAScript(也称为 ES)是 Javascript 所基于的标准。

> Ecma 国际是瑞士标准协会，负责定义国际标准。

1997 年 Javascript (LiveScript)的第一个版本被称为 ES1，然后在 1998 年和 1999 年推出了 ES2 和 ES3。然后出来了 ES4，这是一个真正的惨败，不得不放弃(感谢维基百科)。

2009 年 12 月发布了 ES5，2011 年 6 月发布了 ES5.1。

2015 年 6 月，Javascript 发生了重大变化，ES2015 发布了，名称上的变化已经显而易见。官方名称现在是 ES2015，版本是 ES6，今天我们会发现 ES6 的名称比 ES2015 多，但这不会改变任何事情。这个版本的 Javascript 为 Javascript 编程带来了重大变化，比如类、生成器……因为每年六月都会发布一个新版本的 Javascript。

*   ES2016 (ES7)
*   ES2017 (ES8)
*   ES2018 (ES9)

好吧，对于版本，你只需取正式名称(es 2017–7)的最后一位数字，再加上 1 (ES7 + 1 — ES8)，今年 2019 年(6 月)发布的 Javascript 版本将被称为 ES2019，版本为 ES10 (ES9 + 1)。

## Javascript 工具

从本教程开始，我就一直在说，Javascript 如今被用于我们所知的几乎所有计算机编程领域，web 开发、移动开发、视频游戏、机器学习等等

这里先说两个最热门的领域，即 web 和移动开发。

在 web 上，Javascript 允许我们今天制作全栈应用程序，我们的应用程序将在前端和后端完全用 Javascript 编码，这本身就已经很了不起了。基本上，我们使用后端语言，如 Java、PHP、Python，而在前端，我们使用 Javascript，这使我们在同一个应用程序上使用两种语言。

总是在网络上，Javascript 将允许我们:

*   在用户的浏览器上做一些事情，而不必向服务器发出请求(这需要重新加载页面)，这对于验证表单是很好的
*   动态添加 HTML，编辑页面内容，根据用户的动作改变页面样式
*   在页面上制作动画

现在的网页，不使用 Javascript 是不可能看到的。

现在在移动设备上，Javascript 允许我们为 Android 和 iOS 开发移动应用程序，有了单一的代码库，我们就有了自己的应用程序，不需要为 Android 开发 Java，为 iOS 开发 Swift。

Javascript 因此被广泛使用，如今脸书的移动应用程序(Messenger、insta gram……)都支持 Javascript。

让我们看看一些语法上的 Javascript 风格。

## 分号

在 Javascript 中，分号根本不是强制的，除此之外，我个人更喜欢省略它，你会在我们一起看到的例子中看到它。在这种情况下，您必须非常小心，例如，避免将一条指令写在几行中:

```
return 
1+4 
```

或者以`[`或`(`开始一行，在大多数情况下你会得救。)使用一个 linter (ESLint)来报告错误，不会发生什么严重的事情。

## 注释

在 Javascript 中，您可以使用两种类型的注释，即多行注释:

```
/*
This is a comment in several lines
*/ 
```

还有一行注释:

```
// This is a comment in one line 
```

## 区分大小写

Javascript 是区分大小写的，这意味着`variable`不同于`Variable`，而`Variable`也不同于`VARIABLE`。

重要的是要记住，Javascript 是当今非常流行的语言，如果你有时间学习它，不要犹豫。

这第一部分已经结束了，下一部分再见关于 Javascript 的第二部分，我们将看到 Javascript 中的变量和数据类型。

回头见。