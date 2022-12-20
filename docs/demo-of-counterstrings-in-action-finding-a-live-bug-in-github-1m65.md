# 在 Github 中寻找一个活的 bug

> 原文：<https://dev.to/eviltester/demo-of-counterstrings-in-action-finding-a-live-bug-in-github-1m65>

反串是一种未被充分利用的技术。主要是因为实现它的工具不多。在这篇博文中，我解释了如何在浏览器中使用它们，另外，还可以用它们来查找 Github 中的一个 bug。

## 反字符串

如果您想了解有关计数器字符串的更多信息，请访问:

*   [反字符串算法](https://www.eviltester.com/2018/05/counterstring-algorithms.html)
*   詹姆斯·巴赫在 satisfice.com 的原创作品和复弦作品
*   而且我写了一个 [Chrome 扩展](https://dev.to/page/tools/counterstringjs)
*   我在我的[测试工具中枢](https://github.com/eviltester/testtoolhub)中实现了它

CounterString 是一个类似于`*3*5*7*9*12*15*`的字符串，其中`*`表示紧接在它前面的数字在字符串中的位置。这是一个 15 个字符的反字符串。

这些是有用的，因为如果你把它们粘贴到一个域中，然后被截断，那么很容易看到它们被截断成什么，正如 James Bach 所描述的，自我记录测试数据。

## 使用反字符串

James Bach 的 [PerlClip](http://www.satisfice.com/tools.shtml) 是一个简单的可执行程序，你可以运行它，它将一个计数器字符串复制到你的剪贴板中，用于复制和粘贴到一个字段中。

My [Chrome Extension](https://dev.to/page/tools/counterstringjs) 你右击网页中的一个字段，它将 CounterString 插入到该字段中，并将其输出到开发工具控制台，以便稍后复制和粘贴。

CounterStrings 是有用的，因为如果你把它粘贴到一个字段，它被截断，你可以计算出长度。如果你试图二进制截断来寻找一个验证字符串的长度，那么 CounterStrings 可能是有用的。

## Github

当我为 CounterString 扩展创建演示视频时，我在 Github 上试过。

*   我创建了一个 100 字符的反字符串，
*   添加到 Github 搜索栏。
*   执行了搜索
*   而 Github 表示只会搜索 128 个字符。

但我只输入了 100 个字符。

然后，我使用 CounterString 功能生成其他 CounterString，这帮助我发现当 Github 说“128 个字符”时，它实际上意味着“95 个字符”。

这是一个很好的例子，说明了 CounterStrings 可以帮助您找到的错误类型，以及您如何使用它们来帮助您创建不同的字符串长度，以帮助您确定真正的验证长度，从而在出现缺陷时为您提供支持。

## 视频

[https://www.youtube.com/embed/-Cs80GWeRuY](https://www.youtube.com/embed/-Cs80GWeRuY)