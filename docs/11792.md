# 如何绕过 web 窗体上的无粘贴控件

> 原文：<https://dev.to/eviltester/how-to-bypass-no-paste-controls-on-a-web-form-id8>

仅仅因为一个网站说我们不能粘贴到一个领域，并不意味着我们必须相信它。

受到这篇博文的启发:

[dev . to/claire Parker/how-to-prevent-pasting-into-input-fields-nn](https://dev.to/claireparker/how-to-prevent-pasting-into-input-fields-nn)

Clair Parker-Jones 展示了如何防止人们粘贴到输入字段。这是常见的代码，你会在 StackOverflow 上经常看到。克莱尔的帖子似乎收到了很多批评，但是，人们都这样做，她只是想了解这是如何做到的，并分享这些知识。她还花时间创建了一个 codepen 示例，您可以对其进行探索和实验。

我将示例代码分支到这里:

[https://codepen.io/eviltester/pen/WPpJGo](https://codepen.io/eviltester/pen/WPpJGo)

这是可怕的 UX 模式，但我们一直看到它。作为测试人员，我们必须处理它，或者解决它。

## 如何绕过不粘贴代码？

那么我们如何绕过它呢？

*   在开发工具中检查并删除监听器
*   使用控制台中的代码:

~ ~ ~ ~ ~~document . getelementbyid(" paste-no ")。on paste = { }；
~ ~ ~~~

~ ~ ~ ~ ~~document . getelementbyid(" paste-no ")。onpaste = null
~ ~ ~~~

~ ~ ~ ~ ~~document . getelementbyid(" paste-no ")。on paste = new function(){ }；
~ ~ ~~~

如果它不在框架中，那么创建一个书签就很容易了。创建一个 bookmarklet 是可以做到的，但是比不在框架中要复杂一点。有关书签和框架的信息，请参见[https://www.irt.org/articles/js170/](https://www.irt.org/articles/js170/)

GUI 中的一切都是我们观察、询问和操作的。这就是为什么作为测试人员，我们对技术和工具了解得越多，我们就越能在测试中打开更多的可能性和选择。我们不应该将我们的测试局限于 GUI 中明显的“快乐”路径。

如果你对学习这类东西感兴趣，我有一个在线课程:

[https://eviltester.com/techwebtest101](https://eviltester.com/techwebtest101)

我有一个关于 Patreons 的独家视频，它显示了另一种绕过粘贴的方法(修改 WebElement 值属性)，并在软件测试、风险和 Bug & UX 宣传方面对此进行了更详细的讨论。

[https://www.patreon.com/posts/24482175](https://www.patreon.com/posts/24482175)

## 展示如何粘贴到无粘贴字段的免费视频

[https://www.youtube.com/embed/4DRcv0E2xk8](https://www.youtube.com/embed/4DRcv0E2xk8)