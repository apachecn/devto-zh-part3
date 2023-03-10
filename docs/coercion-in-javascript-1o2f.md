# JavaScript 中的强制。

> 原文：<https://dev.to/uddeshjain/coercion-in-javascript-1o2f>

JavaScript 很奇怪，它有很多意想不到的行为，强制就是其中之一。强制总是社区中争论的话题。一些程序员认为强制是一种特性，而大多数程序员认为它是语言设计中的流程，并建议避免它。但是为了避免某些事情，你应该首先意识到这一点。因此，让我们深入强制，并尝试理解它是什么。

javascript 中的**意外类型转换**称为强制转换。当处理数据时，你需要将它从一种类型转换成另一种类型，比如**数字**到**字符串**或类似的东西，所以为了减少你的工作量，javascript 会自动为你完成，尽管它并不是很好，因为大多数时候会失去控制并导致系统中的错误，这就是为什么大多数人避免它，但这完全取决于你，如果你熟悉这个，你可以使用它，否则就尽量避免它。

现在，胁迫有三种类型。好奇？大家讨论一下。

## **1。数字和字符串**

让我们用例子
来理解

```
console.log('100' - 10) 
```

Enter fullscreen mode Exit fullscreen mode

你怎么看，上面的代码会有什么结果？如果你认为它会产生任何错误等等，我的朋友，你错了，因为输出将是 **90** 。为什么？
因为 javascript 很聪明，它已经知道我们只能减去两个数字，所以它将字符串即**‘100’**转换成一个数字即 **100** 。

好了，你现在明白了，让我们试试另一个例子

```
console.log('100' + 10) 
```

Enter fullscreen mode Exit fullscreen mode

注意上面代码中的 **(+)** 符号，而不是 **(-)** 。你觉得怎么样？输出应该是多少？
如果你认为会是 **110** ，抱歉说你又错了，会是 **10010** ，为什么？
因为在正号的情况下，javascript 没有将 **100** 转换成数字，而是将两者串接起来，产生结果 **10010** 。

## **2。布尔型**

这是其中最麻烦的一个，主要用在 **if** 语句、**循环**和**逻辑**运算中。

所以，让我们考虑这个例子

```
console.log(true + 100) 
```

Enter fullscreen mode Exit fullscreen mode

如果你预测输出将是 **101** ，你变聪明了。原因是 javascript 把**真**当 **1** 和**假**当 **0** ，现在你明白了我很确定你一定能预测下一段代码的输出。

```
console.log(false + 100) // obiously 100 
```

Enter fullscreen mode Exit fullscreen mode

以防你没收到。**假**会被认为是 **0** 所以 **0 + 100** 会是 **100** 。

## **3。相等**

这篇文章快结束了，我希望你已经明白了。现在我们来看看最后一个。

```
console.log('100' == 100) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将返回 **True** ，因为在双等号运算符的情况下，javascript 不会检查左右两边的类型。它只是检查值是否相等。

```
console.log('100' === 100) // False 
```

Enter fullscreen mode Exit fullscreen mode

在三重相等的情况下，结果将是 **False** ，因为三重相等运算符严格检查左侧和右侧的类型。

我希望你明白我的意思。在那之前我会带着新的帖子回来**再见**。

[![Bye](img/8d3281e58f858759f2d604c33dc9b5bd.png)](https://i.giphy.com/media/39qGFvLquTv2A81r0j/giphy.gif)