# encodeURI 和 encodeURIComponent 的区别

> 原文：<https://dev.to/wanoo21/difference-between-encodeuri-and-encodeuricomponent-j3j>

[![Difference between encodeURI and encodeURIComponent](img/ed1c7253f184eba8ea0605c359a0e231.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aEjLfkEX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://yon.fun/conteimg/2019/03/confuse.png)

我非常确定我正确地使用了 **encodeURI** 和 **encodeURIComponent** ，直到我面临一个大问题！英雄联盟😂。当我解决了我的问题，我终于明白了它们之间的区别。所以让我们来了解一下吧！

## 它们是什么

如你所知，在 javascript 中 **encodeURI** 和 **encodeURIComponent** 用于对*统一资源标识符* (URI)进行编码，将某些字符的每个实例替换为一个、两个、三个或四个表示字符的 UTF-8 编码的转义序列。(MDN)

## 区别

区别不大，唯一的区别是 encodeURI()函数编码了*特殊字符*，除了:`, / ? : @ & = + $ #`而 encodeURIComponent()函数编码了*特殊字符*并且在**中增加了 encodeURI 不编码的字符**！

## 什么时候用什么

如果你编码一个字符串放入一个 URL 组件(一个查询字符串参数)，你应该使用 **encodeURIComponent** ，如果你编码一个现有的 URL，使用 **encodeURI** 。很简单！😎

### 一些参考文献:

MDN [编码组件](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)。
T3】MDN[encodeURI](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURI)。

到现在为止，你和他们有过不愉快的经历吗？