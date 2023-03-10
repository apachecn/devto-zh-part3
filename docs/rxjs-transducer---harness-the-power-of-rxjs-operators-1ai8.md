# RxJS 传感器-利用 RxJS 操作员的力量

> 原文：<https://dev.to/rasmusvhansen/rxjs-transducer---harness-the-power-of-rxjs-operators-1ai8>

*使用众所周知的 RxJS 操作符，使用新的微型库 rxjs-transducer 以极快的速度操作数组或可迭代的对象*

到目前为止，大多数 JavaScript 开发人员已经学会使用 Array 的内置方法，如`filter`、`map`、`reduce`、`some`和`every`来以函数式编程风格操作数据数组。这种编程风格的优点是比命令式循环编程更容易推理。然而，它也有一些缺点:

*   这些操作只适用于数组，不适用于迭代。

*   有一组相当有限的操作。值得注意的遗漏是像`take`、`skip`、`first`、`last`、`single`这样的操作。

*   糟糕的性能:当链接多个操作时，它们将各自创建一个中间数组，因此只要有操作符就迭代数组。例如: