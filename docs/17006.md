# TIL:带有回调的 JavaScript replace()命令

> 原文：<https://dev.to/huytd/til-javascript-replace-command-with-callback-3l7h>

当然，这并不新鲜，它在几个世纪前就已经出现在文档[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/String/replace](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)中了，但是我从来不需要做任何复杂到可以使用它的替换，所以我没有注意到它，直到我今天读到一个队友的 pull 请求。

JavaScript 中的`replace()`命令有一个回调函数，为您提供更多的信息，比如匹配的内容、索引和原始字符串。您在回调中返回的内容将被替换为匹配的内容。

这允许您进行更复杂的替换，例如:您想要将字母`"abcabc"`中的字母`"a"`的第二次出现的替换为字母`"$"`。

你将如何为此编写一个正则表达式？如果我们将需求
更改为任何第**次出现的**会怎么样？即使你找到了一个正则表达式解决方案，它是否足够优雅，不会让其他开发人员在维护你的代码时呕吐？

使用带有回调的`replace()`，我们可以写:

```
"abcabc".replace(/a/g, (matched, index, original) => {
  if (index !== 0) {
    return "$";
  } else {
    return matched;
  }
}); 
```

就这样，停止编写复杂的正则表达式，开始使用带有回调的`replace()`
，这让事情变得更简单。你和你的队友，都有生活要过，都有理智要拯救。