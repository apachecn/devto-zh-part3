# ES6 及以后>第 2 部分

> 原文：<https://dev.to/elanandkumar/es6-and-beyond-part-2-p9m>

这篇文章是上一篇文章的第二部分。如果你还没有读过，请在这里阅读:

[![elanandkumar image](img/11071df91771d28c04208dffbdac6280.png)](/elanandkumar) [## ES6 及以后>第 1 部分

### Anand Kumar 6 分钟读数

#es6 #javascript #intermediate](/elanandkumar/es6-and-beyond-4f96)

在本文中，我们将学习承诺，以及使用`async/await`代替承诺来实现异步任务的新的改进/替代方法。

目标是学习以下内容:

*   `Promise`
*   `Promise.prototype.finally`
*   `async/await`
*   `for...await...of`

## 许诺:一个简介

那么，什么是**承诺**？

> 它是一个对象，保证我们在将来某个时候为异步操作返回一个值。

让我们通过理解一个承诺可能具有的状态来更好地理解它。承诺是一个对象(如上所述)可以有 3 种状态:

*   *履行:*当承诺被解决/履行时。
*   *拒绝:*承诺未能兑现时。
*   *待定:*既未拒绝也未履行是待定状态。

下面是如何用 javascript 写承诺的语法: