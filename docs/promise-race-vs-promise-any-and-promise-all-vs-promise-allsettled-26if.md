# 比赛对承诺。任何和承诺。所有对承诺。所有解决

> 原文：<https://dev.to/dance2die/promise-race-vs-promise-any-and-promise-all-vs-promise-allsettled-26if>

*照片由[瑞安·佛朗哥](https://unsplash.com/photos/C6YVD4keMJY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/pinky-promise?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

[2019 年 5 月 8 日 JavaScript (Google I/O '19)](https://www.youtube.com/watch?v=c0oy0vQKEZE) 中的新功能展示了静态承诺组合子方法、 [Promise.allSettled](https://github.com/tc39/proposal-promise-allSettled) 和 [Promise.any](https://github.com/tc39/proposal-promise-any) 的未来/可用功能。

现代浏览器中已经有两种方法可用， [Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 和 [Promise.race](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race) 。

让我们来看看不同之处以及每种方法的工作原理。

## 🚀先决条件

### 🔆承诺定义

我将跳过承诺是什么，直接跳到静态方法，并讨论不同之处。

要点是，承诺是 JavaScript 向您承诺工作将会完成(或者如果工作无法完成可能会失败)的方式。

*如果你熟悉 C#，那就类比* [*任务*](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task) *类。*

有关更多信息，请参考以下文档。

*   [Promise–MDN 上的 JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
*   JavaScript 承诺:谷歌开发者简介

### 🔆承诺状态定义

*   **履行**–承诺成功兑现时。
*   **被拒绝**——当一个承诺落空。
*   **待定**——当一个承诺“[既不履行也不拒绝](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md#states)”时。
*   **Settled**——不是一个真正的国家，而是一个*的总称*，用来描述一个承诺要么被履行，要么被拒绝。
    *   这个术语将在后面用来描述新方法的特征。

有关状态和命运的更详细解释，请参考[状态和命运](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md)。

还有其他静态承诺方法，比如 [Promise.reject](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject) 、 [Promise.resolve](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve) ，但是我将只介绍“组合子”方法，它接受一个可迭代对象作为参数。

## 🚀差异

让我们首先看看现有的和新的组合方法之间的区别。

### 🔅promise . all vs . promise . all 已解决

两者都接受可迭代对象，但是

*   一旦可重复对象中的承诺被拒绝，就立即拒绝。
*   `Promise.allSettled`解析时不考虑 iterable 对象中被拒绝的承诺。

### 🔅承诺。比赛对承诺。任何

两者都接受可迭代对象，但是

*   `Promise.race`可迭代对象内第一个已解决(完成或拒绝)承诺的短路。
*   `Promise.any`短路第一个实现的承诺并继续解决，而不管拒绝的承诺，除非 iterable 对象中的所有承诺都拒绝。

## 🚀对照表

现在让我们看看现有的/即将出现的组合子方法。