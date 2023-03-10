# Flow 0.99:可调用属性、函数静态等等

> 原文：<https://dev.to/wgao19/flow-0-99-callable-properties-function-statics-and-more-5c82>

[封面图片:时间#9 的文字](https://unsplash.com/photos/PwQHfxo3Q2Y)

流量[前几天发布了 0.99](https://github.com/facebook/flow/releases/tag/v0.99.0) ( [对比变化](https://github.com/facebook/flow/compare/v0.98.0...v0.99.0))。该版本包含围绕可调用属性、函数静态、来自 React 组件领域的更多实用程序、错误修复和流服务器性能改进的更改。

我再一次使用这些零碎的东西来理解更多关于 Flow 和 JavaScript 的知识，这一次主要是关于可调用的属性和函数静态，我将在这篇文章中分享。

## 🍾功能静力学

下面是一个导致我们代码库出现新错误的变化:

**[使函数类型静力学更严格](https://github.com/facebook/flow/commit/b868f61026b9b34cb22c3f59e1bcbb65a614c71b)**

> 在这一改变之前，我们乐观地将函数类型的静态类型输入为`any`，假设函数不会被用作对象。实际上，我们的乐观是没有根据的。
> 
> 这个 diff 将函数类型的 statics 对象的类型更改为一个空的、不精确的对象类型，Function.prototype 用于其 **proto** 。就运行时行为而言，这个选择是合理的，同时也允许具有静态的函数通过宽度子类型成为有效的子类型。

是时候回顾一下 JavaScript 了。什么是*函数静力学*？

对于一个 JavaScript *类*，一个 [*静态方法*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes#Static_methods) 是该类的方法，而不是该类的实例。

对于一个 JavaScript 的*函数*，*静态*指的是那些奇怪的时刻，你可能有一个变量，可以通过说函数名点变量名来访问，因为函数在 JavaScript 中也是对象🤷🏻‍♀️.考虑这个带有计数器的问候函数:

```
function greeting(name) {
  if (!greeting.counter) {
    greeting.counter = 0
  }
  greeting.counter++
  return 'hello, ' + name
} 
```

因此，这一变化表明，以前的流程没有对函数静态设置任何限制，也就是说，它们被类型化为`any`。在上面的例子中，这意味着`greeting.counter`是`any`的一个字段，因此不受限制。

您可以键入函数 statics，例如:

```
function greeting(name) {
  (greeting.counter: number)
  if (!greeting.counter) {
    greeting.counter = 0;
  }
  greeting.counter++;
  return 'hello, ' + name;
} 
```

在[中尝试流](https://flow.org/try/#0PTAEAEDMBsHsHcBQjIFcB2BjALgS1uqAOYBOApmXukQBToCGAtmQJSgDeiooNpFVRAHSZYGbGRIAuUOlSMARhJZdQuSDwCEfSrmrDR6cSTadu3bQP1iJoALygADAG4VAXxUXdQkdZIBqPxducmxUEkIAcgALMmg4ABpQCNA-GSYyF1cgA)并注意，如果您将`counter`的注释更改为`string`，流将捕获错误:

```
4:   (greeting.counter: string)
      ^ Cannot cast `greeting.counter` to string because number [1] is incompatible with string [2].
References:
6:     greeting.counter = 0;
                          ^ [1]
4:   (greeting.counter: string)
                        ^ [2] 
```

### 🐿我们代码库中的新错误

然而，在我们的代码库中发生的真正错误有所不同。

我们编写 Redux 动作，将数据封装在一个通常称为*有效载荷*的字段中。我们通常这样标注有效载荷:

```
type ActionPayload = {
  data: string,
  error: string,
} 
```

有时`payload`可以是一个返回包含数据的对象的函数，所以我们也很高兴看到这个:

```
const fetchData = ({
  type,
  payload,
}: {
  type: 'FETCH_DATA',
  payload: () => ActionPayload,
}) => {
  // function body
} 
```

但是，在一些情况下，我们在函数形式中输入了错误的有效载荷，而有效载荷实际上只是对象。所以我们的代码运行良好，没有人注意到注释根本没有意义:

```
const fetchData = ({
  type,
  payload,
}: {
  type: 'FETCH_DATA',
  payload: () => ActionPayload, // <- wrooooong, or not?
}) => {
  return payload.data
} 
```

在 0.99 处，由于函数静态现在被类型化为`{}`，流抱怨我们不能得到`data`，因为函数类型的*静态中缺少`data`。*

类似的场景是动作和动作创建者的混合。它们经常被传来传去，这使得打字变得更加麻烦。我意识到，当我们试图在这个方向上变得聪明时,“心流”真的阻碍了我们。我喜欢这种灵活性，但抱怨会不断地问我这是不是一种好的模式，对此我还没有答案。

## 👾可调用属性语法

**[删除弃用的`$call`属性语法](https://github.com/facebook/flow/commit/cbd64384dccaddaa8461d7850c24419e2217d13e)**

首先，为了不被混淆，这是*而不是*[`$call`实用程序类型](https://flow.org/en/docs/types/utilities/#toc-call)，它帮助您获得给定函数的返回类型，既不被弃用也不被移除。

[弃用的`$call`属性语法](https://github.com/facebook/flow/commit/df8ee96bc84b476f874e300b3cd3c07b430e085b)是这个[可调用语法](https://github.com/facebook/flow/commit/b9db648afe4f2207a49985aa82b90a485a11a900)的前身，新语法的添加和旧语法的弃用都发生在 [0.75](https://github.com/facebook/flow/releases/tag/v0.75.0) 中。

那么它们到底是关于什么的呢？

它联系到我们前面提到的事实，JavaScript 函数也是对象。这里有一个有趣的关于心流的观点:

> 具有可调用属性的对象可以等效地视为具有静态字段的函数。

结果是:

```
// You should be able to call objects with call properties
function a(f: { (): string }, g: { (x: number): string }): string {
  return f() + g(123)
}

// ...and get an error if the return type is wrong
function b(f: { (): string }): number {
  return f()
} 
```

同样:

```
// You should be able to use an object as a function
function a(x: { (z: number): string }): (z: number) => string {
  return x
} 
```

最令我惊讶的#TIL 可能是这个:

```
// Multiple call properties should also be supported
function a(f: { (): string, (x: number): string }): string {
  return f() + f(123)
}

// It should be fine when a function satisfies them all
var b: { (): string, (x: number): string } = function(x?: number): string {
  return 'hi'
} 
```

这是另一个提醒我们关于函数的例子，那些由原型实现的一元链式调用可以归结为函数中的一个字段:

```
// Expecting properties that do exist should be fine
var b: { apply: Function } = function() {} 
```

这是心流拥有的一个非常有趣的特性！这意味着你可以用 Flow 来注释记忆化的函数，而我在学习这些之前从来没有想到过这一点。考虑一个*记忆阶乘* ( [尝试流](https://flow.org/try/#0PTAEAEDMBsHsHcBQiAuBPADgU1AWSwLawCWAXlgCYBiAhgMYqwBOxN0AKpjgLygDeiUKDr0AFlgBc-QaACQAbQB2AVwIAjLEwC6Ules0yAvgBoZ8+SOjQtWgBR6NTAJS7Vj04eR1YigM4pQSHpGFjYpfCIySloGZlYOLlBeRSSAPmkhYkhQWwBCINjQ6AA6ETpxJwyhQOC4tlKxHn5PIRbQLJyCkPiG8qwlLVBc7l5lRQosSGJFSkqBatAmLBRlJhSuupKy8QGjGQ2i3p3FQeSkkdAABlAAflAARlBdUAAqGsL4+1AAWgenGSWKzW7269W2-ROiEMQA))，你可以用流:
来表达它

```
type MemoizedFactorialType = {
  cache: {
    [number]: number,
  },
  [[call]](number): number,
}

const factorial: MemoizedFactorialType = n => {
  if (!factorial.cache) {
    factorial.cache = {}
  }
  if (factorial.cache[n] !== undefined) {
    return factorial.cache[n]
  }
  factorial.cache[n] = n === 0 ? 1 : n * factorial(n - 1)
  return factorial.cache[n]
} 
```

* * *

在这个版本中，围绕可调用属性还有一些变化，我将列出它们的参考:

*   [修复带有`[[call]]`属性](https://github.com/facebook/flow/commit/5d7e01d06b85388882b3214e383d26b73e6de111)的对象的隐式原型
*   [将多个调用属性建模为可调用对象的交集](https://github.com/facebook/flow/commit/b9e26fd3bf7ba10e8f2f8c4e9c90d7f92afe96e1)
*   [交集处理前具体化可调用对象参数](https://github.com/facebook/flow/commit/0938da8d7293d0077fbe95c3a3e0eebadb57b012)
*   [从函数静态中删除`[[call]]`属性](https://github.com/facebook/flow/commit/3c5a41026f8da473b3869e0ef6d84afa0b978816)

这里有几个链接指向一些旧的提交，你可能想看看关于可调用的属性:

*   [在内部槽](https://github.com/facebook/flow/commit/b9db648afe4f2207a49985aa82b90a485a11a900)中存储对象调用属性
*   [增加对`[[call]]`内部插槽属性](https://github.com/facebook/flow/commit/954a72704a6338778c940239573045b28c716488)的支持

## 🦖其他显著变化

还有一些更值得注意的变化，我现在就在这里列出来:

*   [允许箭头返回类型内元组内的函数类型](https://github.com/facebook/flow/commit/65e6d1df078e778d0244d4ee49bd28c741b5af0f)
*   [`React$Config`、`React$ElementRef`、`React$ElementConfig`和`React$ElementProps`现在是效用类型](https://github.com/facebook/flow/commit/2ac4372e31a1f3a03ae963ed7a89b2118b862aad)
*   [`Fragment`的孩子现在可以正确选择](https://github.com/facebook/flow/commit/6dd62e0d9e0dd3e65a07310abc67a022e6335e1f)
*   [存在型`*`已弃用](https://github.com/facebook/flow/commit/72462e4f5849f3e5fe27b15bde54aede8b27b8b8)

如果你发现其中任何一个有趣的问题，并对它们有所了解，请写下来，因为从 0.85 开始，我对心流的学习感觉就像坐在课堂上，没有人问任何问题，但显然没有多少人明白发生了什么🤦🏻‍♀️.私下里，我也不完全确定我说的关于心流的事情。如果你发现任何错误，请务必让我知道。

## 💯下次见

[流量 0.100](https://github.com/facebook/flow/releases/tag/v0.100.0) 也被释放。如果您还没有注意到，发行说明现在附带了 Try Flow 示例。也许增加的数字预示着一个新的时代。