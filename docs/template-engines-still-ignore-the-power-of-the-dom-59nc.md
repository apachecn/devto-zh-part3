# 模板引擎仍然忽略了 DOM 的力量

> 原文：<https://dev.to/kapouer/template-engines-still-ignore-the-power-of-the-dom-59nc>

我需要你今天就离开 php 思维。
为什么？
因为这种想法让你构建“基于字符串”的模板引擎，这是不好的。

显然，这是不安全的，基于字符串的引擎不知道它们在属性、标签名、纯文本或 html 中的什么地方合并数据。
他们很难保持正确，因为 DOM 对他们来说发展得太快了。

DOM 应该在前面，然后是模板。拥抱大教堂。不要尝试:

*   [超级 html](https://www.npmjs.com/package/hyperhtml)
*   因为他们只是在大教堂里闲逛。

我一直在尝试做一些纯粹基于 DOM 的东西:
[https://github.com/kapouer/matchdom](https://github.com/kapouer/matchdom)

然而，我认识到 API 是奇怪的。

如果你有动力，请帮助我改进它！