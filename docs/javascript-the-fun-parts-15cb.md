# JavaScript:有趣的部分

> 原文：<https://dev.to/solkimicreb/javascript-the-fun-parts-15cb>

JavaScript 每年都在快速发展。其中一些从未受到关注，另一些则被遗忘或弃用。让我们把这些放入越来越多的可编辑代码沙箱中。目标是让你玩的时候脸上偶尔带着惊恐。

> 这些例子取自我多年来编写的真实的库或应用程序。最后几个是有争议的，我不鼓励你在你的代码中使用它们。

## 终于

当我必须用一些额外的逻辑包装其他开发者的函数时，我通常使用 [finally](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch#The_finally_clause) 。这样做不会扰乱原来的行为——比如返回值或抛出的错误。

此示例使用性能测量来检测传递的函数。

```
function measure (fn) {
  const start = Date.now()
  try {
    return fn()
  } finally {
    console.log(`${fn.name} took ${Date.now() - start} ms`)
  }
} 
```

[![Edit l9wj5zx20l](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/l9wj5zx20l?module=%2Fsrc%2Fmeasure.js)

## WeakSet 和 WeakMap

这两个提供了真正的私有属性(不像[符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol))。当我必须向其他人的对象追加一些元数据时，我会使用它们。直接改变这些将是不礼貌的，另外，如果对象是[冻结](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)，你就完蛋了。

此示例字符串化传递的对象并缓存结果。内存化加速了操作，WeakMap 的使用避免了对象污染和内存泄漏。

```
const cache = new WeakMap()

export default function stringify(obj) {
  let result = cache.get(obj)
  if (!result) {
    result = JSON.stringify(obj, null, 2)
    cache.set(obj, result)
  }
  return result
} 
```

[![Edit wn1ror8jz5](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/wn1ror8jz5?module=%2Fsrc%2Fstringify.js)

## 代理

[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)可以调整语言行为。大多数时候我用它们来*扩展*语言，偶尔我用它们彻底改变一些默认行为。

这个反应性的例子可以让你创造观察和反应。当内部使用的可观察物发生突变时，反应会自动重新运行。这个片段非常简单，但是一些额外的代码可以让它变得非常简单。

```
let runningFn
const reactions = new WeakMap()

export function observe(fn) {
  runningFn = fn
  try {
    return fn()
  } finally {
    runningFn = undefined
  }
}

export function observable(obj) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      runningFn && reactions.set(target, runningFn)
      return Reflect.get(target, key, receiver)
    },
    set(target, key, value, receiver) {
      Reflect.set(target, key, value, receiver)
      const reaction = reactions.get(target)
      reaction && reaction()
    }
  })
} 
```

[![Edit wn7nz5x7x8](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/wn7nz5x7x8?module=%2Fsrc%2Fobserver.js)

## 新增功能

[new Function()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function) 类似于`eval`，但是它在全局范围内创建函数，这在任何`'strict mode'`声明之外。我们很快就会滥用这个事实。

这个例子得到了`global`对象——不考虑平台——这比它应该得到的要复杂。它也用于[es 2019 global this proposal](https://github.com/tc39/proposal-global)poly fill。

```
const globalThis = new Function('return this')() 
```

> 如果您已经为您的页面设置了 [CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) ，这将不起作用。

## 同

[with](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/with) 扩展了语句的作用域链。遗憾的是，它已被弃用，并且不能在严格模式下工作——包括 ES6 模块。不过，一些小技巧可以扭转乾坤。

这个示例让您编写 Vue 或 Angular 类模板文件，并在传递状态的上下文中呈现它们。

```
export default function compile(template) {
  return new Function("state", `with (wrap(state)) { return \`${template}\` }`)
}

window.wrap = state => new Proxy(state, {
  has: () => true
}) 
```

[![Edit wyv631ry7k](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/wyv631ry7k?module=%2Fsrc%2Fcompiler.js)

它通过以下技巧调整`with`行为:

*   它使用`new Function()` -而不是`eval` -在全局范围内创建函数。全局范围在严格模式之外，所以`with`在那里工作。

*   它使用代理来稍微调整`with`的行为。通常`with`会扩展作用域链，但不会完全替换它。当在传递的对象上没有找到一个属性时，它将在下一个范围内搜索它——在我们的例子中是全局范围。我们不希望全局范围泄漏到模板中，所以我们必须欺骗`with`认为每个可能的属性都存在于传递的对象中。这对于 [`has`代理陷阱](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/has)来说相当简单。

## 一切都在一起

我们意外地创建了一个非常快的轻量级 MVC 框架。

[https://codesandbox.io/embed/72oynq44jq](https://codesandbox.io/embed/72oynq44jq)

请[查看回购这里](https://everythingexcelblog.files.wordpress.com/2016/09/rolled.png?w=656)别忘了留个星！

* * *

我希望你能抽出时间来玩沙盒。如果你创造了一些疯狂的东西，请留下你的评论。

*感谢阅读！*