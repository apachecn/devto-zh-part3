# Javascript 字符串模板的优点和缺点

> 原文：<https://dev.to/dexygen/the-pros-and-pros-of-javascript-string-templates-1g4a>

是的，你没看错，Pro 的和 *Pro 的*(不是 Con 的)。今天早上我提交了包含字符串模板的代码，如下所示:

```
alert(`You've found the solution (${solution}) !!`); 
```

实际上，我是从双引号和连接开始的，所以“You 've”中的撇号不会引起问题。但是如果我从单引号开始，我就不得不避开撇号。

我知道我不应该做字符串连接，所以改成了上面的字符串模板。但是我突然意识到，通过使用字符串文字，你不必在字符串中转义*或者*单引号或者双引号。

那只是一个专业人士。我决定编写一个 JSPerf 来确保没有任何性能影响。但无论如何，在 Chrome 上，[差别微乎其微](https://jsperf.com/string-quoted-vs-template/)。YMMV(你的里程可能会有所不同)但在那之前，我会坚持我的观点:亲的和亲的；)