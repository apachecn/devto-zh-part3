# 理解不熟悉的代码

> 原文：<https://dev.to/joshnuss/parachuting-into-unknown-code-19hg>

我们都想更好地编写代码，对吗？
但是*读取*代码怎么办？

我们读代码的时间比写代码的时间多，所以值得改进。

另外，我们阅读的越好，我们接触到的想法就越多。这最终也会改进我们编写的代码。

这是一个强化循环。

## 由外向内的方法

当遇到不熟悉的代码时，最好先从理解边缘开始，然后向内努力。这就像解决一个难题。

解决难题的最佳方法是找到合作者最少的棋子(边角棋子)，然后整理出合作者最多的棋子(中间棋子)。

[![Puzzle pieces](img/0aff3a0d7d86cd8595f7798d9c9c5ce4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F0BX-OPQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ksmpgyc65icy4o1oizld.png)

例如，在一个 web 应用程序中，您可以将一个角落部分想象成**路线**(入口点)或**数据库模式**(出口点)。

内部部分是有更多合作者的部分，如控制器和模型。

## 读取代码

使用这种方法，检查 web 应用程序的良好顺序应该是:

1.  **所属** : `Gemfile`，`package.json`或`mix.exs`
2.  **输入**:路线或图表定义
3.  **输出**:数据库模式定义或外部 API 调用
4.  **算法**:模型、控制器、类别或模块

## 读取文件

这是读取文件的类似方法。从外面开始，向内推进:

1.  **所属** : `import`，`include`或`require`
2.  **输入&输出**:模块或类的 API，参数和返回值
3.  **算法**:函数体(中间)

先关注小文件，再关注大文件。较小的文件要么依赖于较大的文件，要么协作者较少。这让他们更容易理解。

就像生活一样，从小处开始，然后一步步向上。

下面是如何找到最短的文件:

```
# find shortest files by extension
wc -l $(find . -name *.js) | sort -n 
```

Enter fullscreen mode Exit fullscreen mode

# 测试

看一下测试。它们用输入参数和预期输出的例子显示了所有的入口通道。它们是无价的。

# 修补模式

随着时间的推移，你可以通过在 REPL 修修补补来强化你的学习。

在 Rails 中，这意味着进入`rails console`，在 JavaScript 中使用`node --require index.js`，在 Elixir 中使用`iex -S mix`。

# 调试一下

使用调试器逐句通过代码。这是一个很好的方式来看关键路径的行动。

你可以在 Ruby 中使用`binding.pry`或`byebug`来实现，在 JavaScript 中使用`ndb`，或者如果你使用的是一种并发语言，比如 Erang/Elixir，可以使用`observer:start()`或`sys:trace(pid, true)`。

让自己熟悉语言的调试和跟踪工具是一项总会有回报的投资。

## 结论

阅读代码是一种技能，这意味着它需要练习来提高。

如果你第一次没有理解所有的事情，也不要担心。片面理解正在走向全面理解。

读码快乐！