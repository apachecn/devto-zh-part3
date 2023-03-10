# 为什么我们不再使用 var？

> 原文：<https://dev.to/codingsam/why-don-t-we-use-var-anymore-c71>

如果你过去用 Javascript 编码，你会大量使用 [*var*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var) 关键字。没有其他方法来声明变量。真的很简单。你唯一需要做的事情就是这样:

```
var x = 3; var y = 'hello'; // etc... 
```

从 ES6 开始，新增了两个关键词， [*const*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) 和 [*let*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let) 。第一个有点不言自明，它允许你定义一个常数。如果有:

```
const x = 2; 
```

不能给常数 *x* 重新赋值。有许多编程语言允许开发人员定义常量。这是编写不易出错的代码的好方法。然而，还有这个“ *let* 关键字，它允许你声明变量。这就是你所需要的，只是另一个和 *var* 一样的关键字，对吗？为什么不是只有“var”和 *const* ？

> 为什么我们不再使用 var？

要回答这个问题，你需要知道 *var* 到底是如何工作的。在 Javascript 中，在 ES6 之前，没有**块范围**。你有**功能范围**代替。我们来分解一下这两个概念。

### **封锁范围**

当您声明一个变量或函数时，它将在声明它的块中被访问。大多数编程语言都有这种类型的作用域，例如 Java。参见下面的代码片段:

```
public class Example () {
  public void processArray (String[] array) {
    for(int i = 0; i < array.length; i++) {
      System.out.println(array[i]);
    }

    System.out.println("I cannot use the variable i here");
  }
} 
```

只能在 for 循环内部使用变量 *i* 。它不存在于那个街区之外。在 Java 中，每次使用“{”都是在创建一个新的块，而“}”意味着关闭那个块。当然，如果在块外声明变量，也可以在块内使用。

让我向您展示下图，它说明了块范围在此示例中是如何工作的:

[![](img/00434eb1b687d309d635e8f6d0a4108e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4zdQvcOv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/322/1%2AWQqkmBqRLovbfM1fp-2XlQ.png) 

<figcaption>块范围使用示例类。</figcaption>

每个矩形都是一个范围。“子”作用域可以访问“父”作用域中的函数和变量，但是“父”不能访问“子”。在本例中，示例类是 *processArray* 方法的父类，该方法是循环块的*的父类。示例类不能访问属于 *processArray* 方法的任何内容，该方法也不能访问属于循环*的*的任何内容。然而, *for 循环*可以访问 *processArray* 方法中的任何内容以及 *Example* 类中的任何内容，例如，变量或任何其他方法。这是大多数开发人员习惯使用的范围。*

### **功能范围**

与 Java 不同，Javascript (ES5)基于函数创建作用域。这意味着，一旦你在函数中声明了一个变量，你就可以在函数中的任何地方使用它。

```
function processArray (array) {
  for(var i = 0; i < array.length; i++) {
    console.log('Element ', array[i]);
  }

  console.log('I can use variable i outside the loop ', i);
} 
```

当然，你也有全球范围。每次在函数外部声明变量时，它都属于全局范围。

让我向您展示另一个图表，但这一次是针对函数范围的:

[![](img/35b0e22555926af126dfc85fc0cb4712.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qtu5j3Y8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/322/1%2A3JueUzrZxCjytZCh9_sDuw.png) 

<figcaption>函数范围为 processArray 函数</figcaption>

看起来简单多了吧？但是 for 循环在哪里呢？

for 循环是一个块，但是这里没有块作用域，这就是为什么，它没有自己的作用域。

### 为什么我们不再使用 *var* 关键字？

都是关于范围的！大多数编程语言都有块范围。为什么？因为它不容易出错。您可以在块(if 语句、for 循环等等)中声明变量，而不用担心覆盖一些先前声明的变量。

让我给你看一个例子。假设您正在实现一个打印矩阵每个位置的函数。您将在循环的另一个*中为循环*编写一个*。在 ES5 中，只有 *var* 关键字可用，初学者可以写类似这样的代码:* 

```
function printMatrix (matrix) {
  for (var i = 0; i < matrix.length; i++) {
    var line = matrix[i];
    for (var i = 0; i < line.length; i++) {
      var element = line[i];
      console.log(element);
    }
  }
}

var matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];

printMatrix(matrix); 
```

输出将是:

[![](img/0072a7fad0a7633bc2631bdbd498e880.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFkRgMst--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/84/1%2ABvtUBy7SAw6t9pGK-mDATQ.png)

<figcaption>ES5</figcaption>

中 printMatrix 函数的输出

它只记录了第一行。为了帮助您理解正在发生的事情，让我向您展示这个示例的范围图:

[![](img/56995c628f4da616ba0852b91f826803.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mRPYNBEk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/322/1%2AijeggeoI_dbSCgIrwQrvBQ.png) 

<figcaption>在 printMatrix ES5 版本中分解范围</figcaption>

*printMatrix* 函数内的所有变量都在同一层。两个 *for 循环*实际上使用的是同一个变量 *i* ！

发生了什么事？当我几年前开始用 Javascript 编码时，我写了很多这样的代码，因为我以前用 Java 编码过很多，Java 有块作用域。所以，我认为如果我在循环中声明了一个变量，它将会留在那里…但实际上不是。

在经历了许多错误和挫折之后，我了解到 Javascript 没有块范围。只有函数作用域。但是，即使我知道了，我还是忘记了很多次！这是一件很容易忘记的事情。通常的预期是，在第二个 for 循环中，您正在创建一个新的变量。但你不是。你只是覆盖了循环的第一个*中的变量 *i* 。运行第二个循环后，将再次评估第一个循环的条件，但变量 *i* 现在的值为 3(矩阵中第一行的大小)，等于矩阵长度(3)。条件返回 false，循环停止。这就是为什么只有矩阵的第一行被记录。*

> 在 Javascript 中，使用多少次关键字“var”并不重要。如果在同一个函数中有相同的名字，那么你指向的是同一个变量。

这个函数范围可能是许多错误的来源。幸运的是，Javascript 一直在变化，现在我们有了 ES6 和更多。有两个“新”关键字， *const* 和 *let* ，它们允许你分别定义一个常量和一个变量。它们都在块范围内工作，这意味着，如果变量或常量在块内声明，它们对“父”块不可用。

让我们利用这些新的关键字重写前面的例子，记录矩阵。如果你用 *let* 替换 *var* 并添加 *const* 用于你知道在初始化后需要改变的东西…

```
function printMatrix (matrix) {
  for (let i = 0; i < matrix.length; i++) {
    const line = matrix[i];
    for (let i = 0; i < line.length; i++) {
      const element = line[i];
      console.log(element);
    }
  }
}

const matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];

printMatrix(matrix); 
```

输出将是:

[![](img/e08401a2a295fd77b5cdbc96cbd4f03f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fLunlyAV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/84/1%2AQn2GnKEM3bIgxec14dc5aQ.png)

<figcaption>ES5</figcaption>

中 printMatrix 函数的输出

矩阵中的所有位置都被记录了下来。它刚刚工作，我只需用*让*和*常量*关键字替换*变量*！因为在循环的每个*中，我实际上创建了一个不同的变量 *i* 。在第二个循环中，第一个 *i* 不会被覆盖。*

让我们看看在引擎盖下发生了什么:

[![](img/8ad0a62c5f852dca350160b53d8813b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G2lSn2tL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/322/1%2A71bRLyJxbpXYrAFc5xzJTA.png) 

<figcaption>在 printMatrix ES6 版本中分解范围</figcaption>

看起来有点复杂，但是这给了你块的范围，并且循环的每个*都有自己的变量 *i* 。在过去的 Javascript 中，您需要给出不同的名称来避免这种命名冲突。但是说真的，当你需要做一个 *for 循环*来迭代一个数组的时候，你脑海中出现的第一个名字，对于当前的数组索引值是“I”，不是吗？*

**只是小注**:我知道你有 [*forEach*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) ， [*map*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ，还有更多处理数组的函数。这只是一个简单的例子来展示 *var* 、 *let* 和 *const* 关键字是如何工作的。

### 结论

*let* 和 *const* 不仅仅是两个新的很酷的关键字，它们还引入了块作用域，允许我们编写干净且不易出错的代码。

为什么我们不再使用 *var* 了？因为现在有了一个更好的声明变量甚至常量的方法…用块作用域！在块内声明变量时，您无需三思。我认为使用块作用域比使用函数作用域更容易。不鼓励使用 *var* 。例如，如果您使用 [ESLint](https://eslint.org/) 来检查您的代码，您可以配置一个“ [no-var](https://eslint.org/docs/rules/no-var) ”规则，如果有任何 *var* 被使用，它将抛出一个错误。

请记住，你应该使用 transpiler 工具，如 [Babel](https://babeljs.io/) ，将你的代码从 ES6 转换到 ES5，以确保它可以在任何浏览器中运行。并非所有的浏览器都支持完整的 ES6 规范。

拥抱*让*和*常量*让*变量*永远走下去！

我希望你喜欢并学到了一些东西。编码快乐！:)

让我知道你的想法，并关注我以获得更多关于开发人员的精彩内容:)

*   [编码 Sam](https://medium.com/u/58cf94a36c97) @介质
*   [codingsam @ Twitter](https://twitter.com/codingsam01)
*   [codingsam01 @ Instagram](https://www.instagram.com/codingsam01)