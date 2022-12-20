# Java:使用 Stream Reduce 的嵌套函数

> 原文：<https://dev.to/msosto/java-nesting-functions-with-stream-reduce-4bgk>

# Java:带 Stream Reduce 的嵌套函数

一个不太为人所知的流操作符是 *Reduce* 。我在大学期间研究过 Haskell，*减少*强烈的联想到 Haskell 的函数 *[折叠](https://wiki.haskell.org/Fold)* 。

# 什么是*减少*？

*给定一个元素列表和一个函数(又名**累加器**，得到一个结果值。*
简单的例子:求一列整数的和。
`Integer result = Stream.of(1, 2, 3, 4, 5).reduce(0, (a, b) -> a + b);`
即会使: **1+2+3+4+5+0**

但是，Streams api 已经解决了这个问题:
`Integer result = IntStream.of(1, 2, 3, 4, 5).sum();`

# 复合函数同 Reduce

假设我们有一个函数列表，我们想构建一个包含列表中所有函数的函数。让我们看看:
首先，我们有:
`List<Function> functions = [f1,f2,f3,f4,f5]`
，我们想要这样一个函数:
`Function resultFunction = (X) -> f5( f4( f3 ( f2( f1(X) ) ) )`

## 如何才能轻松构建一个复合函数？

首先，我们将使用 *Function::然后用*方法将每个函数包装在另一个函数中。
然后，我们调用 *reduce* ，第一个参数是*起始值*，因为我们在构造函数时使用了**幂等**元素，在本例中，它是[恒等函数](a%20->%20a)。

```
 List<Function> functions = [f1,f2,f3,f4,f5];
 Function compositeFunction = functions.stream().reduce(a -> a, Function::andThen) 
```

**如果我们想按列表的逆序合成呢？**
记得数组是:
`List<Function> functions = [f1,f2,f3,f4,f5]`
现在我们需要这样一个函数:
`Function resultFunction = (X) -> f1( f2( f3 ( f4( f5(X) ) ) )`

我们必须将累加器改为 Function::compose

```
 List<Function> functions = [f1,f2,f3,f4,f5];
 Function compositeFunction = functions.stream().reduce(a -> a, Function::compose) 
```

总共*化简*、*函数::合成*和*函数::然后*构思一个功能强大的函数合成工具，用处很大。