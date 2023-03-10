# 如何避免空白字体

> 原文：<https://dev.to/konstantin/how-to-avoid-blank-font-41p6>

加载自定义字体有点棘手。关于如何使用`@font-face` : [防弹@font-face](https://www.paulirish.com/2009/bulletproof-font-face-implementation-syntax/) 已经有一篇很棒的文章了。

不幸的是，许多浏览器在加载自定义字体时的默认行为是在加载自定义字体之前隐藏文本，这意味着用户看不到内容(这可能是他最初访问您的网站的原因)。

## 输入`font-display`属性

所有现代浏览器(除了 edge)都支持吗？它允许你在以下四种字体加载策略中进行选择:

*   街区
*   交换
*   撤退
*   可选择的

`block`行为意味着用户看到空白字体，直到自定义字体被加载，随后被替换。

`swap`行为立即显示文本内容，并在后台加载自定义字体。

`fallback`与 block 基本相同。不同的是，它给了更少的时间来加载字体。

仅当字体已经安装在您的操作系统中时，才尝试交换字体。

## 哪个适合我

对于这个网站，我决定选择更受欢迎的选项`font-face: swap`，但最佳选项总是取决于网页加载的上下文。