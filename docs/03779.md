# JavaScript 中的错误以及如何避免这种常见错误

> 原文：<https://dev.to/michi/iifes-in-javascript-and-how-to-avoid-this-common-error-5b0e>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/javascript-iifes)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

由于 JavaScript 还不支持顶级等待，典型的节点索引文件可能看起来像这样

```
const http = require('http')

(async () => {
  // await ...
})() 
```

Enter fullscreen mode Exit fullscreen mode

我们需要 http 库，然后有一个立即调用的函数表达式(IIFE ),这样我们就可以使用异步 await。

使用 IIFEs，我们编写函数并立即执行它们。这就是为什么函数中发生的一切都停留在函数内部，而不能从函数外部访问。这也是到目前为止在顶层使用 await 的第**种方式。**

# 问题

我确信你们中的许多人已经掉进了这个陷阱，因为上面的代码实际上已经崩溃了。

> `Uncaught TypeError: require(...) is not a function`

崩溃的原因是因为 JavaScript 试图执行这个(尝试在你的编辑器中格式化上面的代码以获得相同的结果)

```
const http = require('http')(async () => {
  // ...
})() 
```

Enter fullscreen mode Exit fullscreen mode

它期望`require`方法返回一个函数，在这个函数中我们传递一个异步函数，然后我们执行这个函数的结果。🤯

当你有两个连续的生命时，这个错误就变得特别难以捕捉。

> `Uncaught TypeError: (intermediate value)(...) is not a function`

# 常见变通办法

有一些常见的解决方法，都是告诉 JavaScript 生活确实是一种新的陈述，最著名的是

```
const http = require('http')

void (async () => { // < note the void at the beginning

})() 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const http = require('http'); // < note the semicolon

(async () => {

})() 
```

Enter fullscreen mode Exit fullscreen mode

甚至

```
const http = require('http')

!(async () => { // < note the exclamation mark

})() 
```

Enter fullscreen mode Exit fullscreen mode

# 标签

上述解决方法并不新鲜，但这里有一些你可能还没见过的东西。

```
const http = require('http')

IIFE: (async () => {

})() 
```

Enter fullscreen mode Exit fullscreen mode

是的，标签也可以。您可以在任何语句前放置标签。在这一点上，只要符合语法，我们可以用我们想要的任何东西替换`IIFE`。如果它作为一个变量名，它就作为一个标签标识符。

```
一か八か: 1 + 1 
```

Enter fullscreen mode Exit fullscreen mode

[标签](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)其实挺有意思的。请看下面摘自 MDN 的代码片段。

```
foo: {
  console.log('this will be executed');
  break foo;
  console.log('this will not be executed');
}
console.log('this will be executed as well'); 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

由于标签并不广为人知，坚持使用分号或 void 可能更好，但这仍然很有趣。我喜欢他们给生活添加一些文档。好吧，让我们再等一会儿最高级别的等待。