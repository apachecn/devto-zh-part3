# 深入探究 RxJS 中的管道函数

> 原文：<https://dev.to/miloszpp/deep-dive-into-pipe-function-in-rxjs-3bmi>

RxJS 的第 5 版引入了*可释放*(也称为*可管道化*)操作符的概念。版本 6 更进一步，摒弃了调用操作符的旧方式(方法链接)。

您可能已经使用了`pipe`函数。但是你真的了解它是做什么的吗？

**这篇文章最初发表在[我的博客](https://codewithstyle.info/deep-dive-pipe-function-rxjs/)上。**

## 排版功能

RxJS 通常被称为**功能反应式编程**库。你会从中发现许多函数式编程的灵感，这并不奇怪。其中之一就是`pipe`函数。

看看下面这段代码:

```
const getElement = 
    (id) => document.getElementById(id);

const getValue = 
    (element) => element.value;

function logElementValue(id) {
  const el = getElement(id);
  const value = getValue(el);
  console.log(value);
} 
```

Enter fullscreen mode Exit fullscreen mode

`logElementValue`函数接受一个`id`并将提供的`id`的元素值记录到控制台。

你能在这个函数的实现中看到一个模式吗？首先，它用`id`调用`getElement`，并将结果存储在`el`中。接下来，结果被传递给`getValue`，它产生新的结果`el`。最后，`el`被传递到`console.log`。

这个函数所做的只是简单地将一个函数的结果作为参数传递给另一个函数。

有没有更好更简洁的方法来实现这个功能？

假设我们只有两个函数(`getElement`和`getValue`)。我们将实现一个名为`compose`的通用函数，它将把`getElement`的结果传递给`getValue`。

```
const compose = (f, g) => x => g(f(x)); 
```

Enter fullscreen mode Exit fullscreen mode

这个定义非常简单，但是可能需要一些时间来解析。我们已经定义了一个函数，它接受两个函数`f`和`g`(在我们的例子中是`getElement`和`getValue`)并返回一个新函数。这个新函数将接受一个参数，将其传递给`f`，然后将结果传递给`g`。

这正是我们所需要的！现在我可以重写`logElementValue` :

```
function logElementValue(id) {
  const getValueFromId = compose(getElement, getValue);
  const value = getValueFromId(id);
  console.log(value);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 两个以上功能怎么样？

但是，等等！一旦我们得到调用`getValueFromId`的结果，我们立即将它传递给`console.log`。所以这是相同的模式。我们可以这样写:

```
function logElementValue(id) {
  const getValueFromId = compose(getElement, getValue);
  const logValue = compose(getValueFromId, console.log);
  logValue(id);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果`compose`可以承担任意数量的功能，生活将会简单得多。我们能做到吗？确定:

```
const composeMany = (...args) => args.reduce(compose); 
```

Enter fullscreen mode Exit fullscreen mode

又一个脑筋急转弯！`composeMany`采用任意数量的函数。它们存储在`args`数组中。我们`reduce`超过`args`组合每一个函数，其结果是组合前面的函数。

总之，results 是一个函数，它接受任意数量的函数，并将把`N-th`函数的结果传递给`(N+1)-th`函数。

但是，我们这样做取得了什么成果呢？

```
function logElementValue(id) {  
  const logValue = composeMany(getElement, getValue, console.log);
  logValue(id);
} 
```

Enter fullscreen mode Exit fullscreen mode

哪个可以更简化:

```
const logElementValue = composeMany(getElement, getValue, console.log); 
```

Enter fullscreen mode Exit fullscreen mode

是不是很酷？我们已经大大简化了代码。现在`logElementValue`做什么已经很清楚了。

而且顺便说一下——`composeMany`只是 a 想出来的一个名字。正式名称是`pipe`！

```
const logElementValue = pipe(getElement, getValue, console.log); 
```

Enter fullscreen mode Exit fullscreen mode

## 回 RxJS

下面举一个 RxJS 中`pipe`用法的例子。

```
number$.pipe(
    map(n => n * n),
    filter(n => n % 2 === 0)
); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以用不同的方式来写:

```
const { pipe } = rxjs;

const transformNumbers = pipe(
     map(x => x * x),
     filter(x => x % 2 === 0),
);

transformNumbers(number$).subscribe(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

而结果是一模一样的！如您所见，RxJS 中的`pipe`函数的行为与我们在文章第一部分中定义的`pipe`函数完全相同。它接受许多函数，并通过将一个函数的结果作为参数传递给另一个函数来组合它们。

你可能会说这与前面的例子不同，因为这里我们调用了`map`和`filter`，而不是简单地传递它们。实际上，`map`和`filter`都会返回函数。我们不是在编写`map`和`filter`本身，而是通过调用它们返回的函数。

你可以在这里查看 RxJS 是如何实现`pipe`功能[的。](https://github.com/ReactiveX/rxjs/blob/94156a214f905555b6e57bc3f7cf965629028406/src/internal/util/pipe.ts)

## 管道操作员

我们的函数是一个非常有用的概念，它可以作为一个单独的操作符添加到 JavaScript 语言中！

这意味着上一篇文章中的例子可以用更简单的方式编写:

```
const logElementValue = getElement |> getValue |> console.log; 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到提案的详细内容[。](https://github.com/tc39/proposal-pipeline-operator)

## 总结

我希望这篇文章能帮助你理解`pipe`函数是怎么回事。您现在应该对使用它感到更舒服了！

RxJS 从应用操作符的传统面向对象方法迁移到管道方法的事实表明了函数式编程的影响力在今天是多么的强大。我觉得太棒了！

如果你更喜欢`pipe`函数而不是传统的方法链接，请在评论中告诉我。

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的书！

[高级类型脚本](https://typescriptmasterclass.com)