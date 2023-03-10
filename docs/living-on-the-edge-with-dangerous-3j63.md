# 生活在“危险”的边缘

> 原文：<https://dev.to/dance2die/living-on-the-edge-with-dangerous-3j63>

因为我一直在使用[风格的组件](https://www.styled-components.com/)(以下简称 SC)，并且一直想知道它背后的魔力。

谢天谢地，麦克斯·斯托伊伯(SC 的创始人)写了一篇文章，[Styled-Components](https://mxstbr.blog/2016/11/styled-components-magic-explained/)背后的魔力，关于 Styled Components 如何与[标记的模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)一起工作。

在文章的结尾，马克斯用下面的句子结束了文章。

> 我非常兴奋地看到人们提出的标记模板文字的其他用例！
> 
> 马克斯·斯托伊伯

所以我创建了 [dangerous](https://www.npmjs.com/package/dangerous) ，一个带标签的模板文字函数，它接受一个危险的文本(比如恶意的用户输入)并以 SC like 风格设置一个组件的内部 HTML。

## 先决条件

我将跳过标记模板文字是什么或者它是如何工作的，因为 Max 已经很好地解释了。

## ☢什么是“危险”？

当您将字符串传递给组件时，它们被编码以防止恶意用户输入/脚本。

React 团队已经很难设置组件的`innerHTML`来帮助开发者意外设置恶意字符串，以防止 [XSS 攻击](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))。

但是`dangerous`使得设置一个`innerHTML`值变得容易，并返回一个带有不安全 HTML 字符串的组件。