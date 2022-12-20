# JavaScript 中的空检查

> 原文：<https://dev.to/wolfhoundjesse/null-checking-in-javascript-lc4>

在今天的*冒险遗产代码*中，我遇到了以下这些:

```
if(
  tokenInfo &&
  tokenInfo !== undefined &&   
  tokenInfo !== null &&
  tokenInfo !== ""
  ) 
```

Enter fullscreen mode Exit fullscreen mode

我明白——我们需要防止事情出错，但一看到这种情况，我就想起了直升机育儿法。此外，它不是真正的空检查，而是未定义和空字符串检查。这是您可以从 TypeScript 中获得的另一个好处——如果您发送了错误的信息，它不会编译，让您安心。

我采纳了 Patricia Aas 的建议，我选择在开发它的时候把它框在开发人员的心智模型中，而不是过分挑剔。毕竟只是一种看法！

你有什么想法？如何检查空值或空值？奖金，如果你用其他编程语言的例子来评论！