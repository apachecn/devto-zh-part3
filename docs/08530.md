# JavaScript 类方法中的扩展语法陷阱

> 原文：<https://dev.to/dance2die/spread-syntax-gotcha-in-javascript-class-methods-1jmb>

*照片由[杰伦](https://unsplash.com/photos/dkFJST9zZZo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/pokeball?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄——不要被抓*

## *小心使用 React 钩子暴露可变数据结构*

当您扩展类的对象实例以公开方法时，方法可能不会被复制。

假设您有一个`Trie`类，

，您想通过使用语法 spread 返回一个新对象来使它不可变。

***不是个好主意！稍后解释。*T3】**