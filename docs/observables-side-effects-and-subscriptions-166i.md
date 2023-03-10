# 可观察性、副作用和订阅

> 原文：<https://dev.to/eyassh/observables-side-effects-and-subscriptions-166i>

我之前关于在 Angular 中使用`AsyncPipe` 和[数据刷新模式的文章暗示了一些常见的处理可观测量的反模式。如果说我的建议中有什么共同点的话，那就是:**延迟将可观察对象解包为其标量类型**当执行逻辑时，您可以重写为无副作用，将有副作用的代码留给订阅回调和其他下游逻辑。](https://blog.eyas.sh/2018/12/data-and-page-content-refresh-patterns-in-angular/)

我之前的两篇文章关注的是用户可以从以可见形式处理更多的对象生命周期中获益的情况。换句话说，观察对象被订阅和解包*太快*的情况。相反，我建议使用类似于`map`、`switchMap`、`filter`等操作符来转换可观察值。利用这种形式提供的力量。在 Angular 的情况下，它提供了 [`AsyncPipe`](https://angular.io/api/common/AsyncPipe) ，负责模板代码中有副作用的步骤(实际上是渲染页面)。

这种思路也有一些例外，即 [`do`和`tap`](http://reactivex.io/documentation/operators/do.html) 是反应性运算符，专门针对有副作用的函数。我将在以后的文章中讨论使用`do` / `tap`的正确和不太正确的原因。但是我将提到日志记录、错误报告和缓存等纯函数，作为副作用的一个有效应用。

让我们探讨几个案例:

# 1。显示由可观察值表示的数据

假设我有两个 Observables 用存储格式(比如 JSON)包装了某个对象，我想显示它。

## 解包一个可观测的*太早*

```
let customerName: string;
let customerBalance: number;

nameObservable.subscribe(name => {
  customerName = name;
  if (customerName && customerBalance) {
    processAndDraw();
  }
});
balanceObservable.subscribe(balance => {
  customerBalancer = balance;
  if (customerName && customerBalance) {
    processAndDraw();
  }
});
function processAndDraw() {
  alert(`${customerName}: $${customerBalance.toFixed(2) USD`);
} 
```

如果一个调用者过早地解包一个可观察对象，这意味着他们正在处理标量，通过全局状态传递东西。开发人员可能会在处理更改时遇到困难，例如添加第三个数据源来显示。

## 拆包可观察到的*来不及*

```
combineLatest(nameObservable, processAndDraw).pipe(
  map(([name, balance]) => {
    alert(`${name}: $${balance.toFixed(2) USD`);
  })
).subscribe(); 
```

一方面，这个要短很多，更有表现力！这实际上是将`Observable<[string, number]>`映射到一个`Observable<void>`中，当*订阅了*时，这个`Observable<void>`会产生副作用。然而，订户不知道仅仅通过查看类型或签名将会发生什么动作。即使按原样使用上面的代码片段，也很容易忘记最后一个`.subscribe()`调用，它——假定 Observables 在默认情况下是懒惰的，只有在订阅时才执行有用的操作——使整个代码片段变得无用

副作用对运营商不利的最后一个原因是:这些副作用可以根据有多少不同的订户在收听一个可观察的事件而被执行任意次数。

## 更好的取舍

```
combineLatest(nameObservable, processAndDraw).pipe(
  map(([name, balance]) =>
    `${name}: $${balance.toFixed(2) USD`
  )
).subscribe(text => alert('Text')); 
```

# [完整篇](https://blog.eyas.sh/2018/12/observables-side-effects-and-subscriptions/)中描述的其他用例

1.  通过`Subject`避免不必要的间接
2.  当`switchMap`或`flatMap`可以订阅时

# 总结

一个可观测值从源到最终结果要经过一系列的变换算子:

1.  可取消直通；取消对结果可观察对象的订阅将取消为此目的开放的任何基础订阅。
2.  独立可组合的；和
3.  一个无处不在的不可变 API，它为调用者提供了操作返回值的灵活性。

我认为副作用是一个很好的一阶启发，只要它能合理地保持在一个合成的可观察范围内。需要时，像 do 和 tap 这样的操作符有时会有意义。

[在 blog.eyas.sh](https://blog.eyas.sh/2018/12/observables-side-effects-and-subscriptions/) 阅读原文全文。