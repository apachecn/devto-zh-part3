# Javascript 私有字段来了

> 原文：<https://dev.to/wparad/javascript-private-fields-are-coming-52ei>

结果是节点阶段 3 现在我们将拥有私有类属性。正如你所想的那样:

```
class Point {
    private int x = 0;
    public int y = 0;
} 
```

开个玩笑，实际上

```
class Point {
  #x;
  y;

  constructor(x, y) {
    this.#x = x;
    this.y = y;
  }

  equals(point) {
    return this.#x === point.#x && this.y === point.y;
  }
} 
```

排队一堆 WTF 时刻。

所以我开始四处寻找一个解释，因为肯定有一个好的理由。有很多人试图解释，但他们还没有走到那一步。我遇到一个人试图解释这个问题:

[Jamie.build](https://jamie.build/javascripts-new-private-class-fields.html)

原因:
(1)因为#封装(见上面的“封装”一节)，我们需要允许公共和私有字段同时同名。所以访问私有字段不能只是普通的查找。

等等，为什么？为什么我们需要允许`private`和`public`字段同名。

(2)私有字段将无法支持第二种语法(因为它需要是静态的),这可能会导致混乱

这句话导致了混乱，什么需要是静态的，我们是在说私有属性需要是静态的吗？不，它们显然不是静态的，因为每个实例都有不同的属性。

(3)我们需要支持`this.#x === other.#x && this.#y === other.#y;`

不，你不知道，等等，你怎么知道`other`有一个`#x`

```
class Point {
  #x;
  equals(otherPoint) {
    return this.#x === otherPoint.#x;
  }
} 
```

你是在告诉我，如果`otherPoint`是一个点，这个函数可以工作，但是如果`otherPoint`不是一个点，那么这个函数就会神奇地崩溃。这意味着我们将对方法中的 javascript 属性进行运行时类型检查。

哇！

所以我们对私有属性使用`#`的原因是为了支持没人需要或者没人能理解的功能，比如“否则会很混乱”

[下面是官方推理](https://github.com/tc39/proposal-class-fields/blob/master/PRIVATE_SYNTAX_FAQ.md#why-does-this-proposal-allow-accessing-private-fields-of-other-instances-of-the-same-class-dont-other-languages-normally-forbid-that)

我希望有人能用通俗的语言解释一下，因为这听起来很像“Java 允许这样做，所以我们也应该这样做。”实际上，我不知道有多少种语言允许这样做，但我天生就认为这是被明确禁止的。它不是公共属性，为什么该类的其他实例也可以访问它。

实际上，github tc39 提案的其余部分甚至都不能证明其合理性。因此，看来我们将无缘无故地陷入这种无意义的状态。