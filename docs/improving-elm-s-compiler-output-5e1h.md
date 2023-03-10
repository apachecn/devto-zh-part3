# 改进 Elm 的编译器输出

> 原文：<https://dev.to/robinheghan/improving-elm-s-compiler-output-5e1h>

榆树很快。

这并不是因为编译器的任何创新。事实上，Elm 编译器几乎不做任何优化。

Elm 之所以快，是因为它编译成了 Javascript，这是来自全球各地的敬业而有才华的工程师十多年来一直在优化的。

但这里有一个问题:Elm 编译器输出的 Javascript 能让浏览器轻松优化它吗？如果不能，改变编译器输出 Javascript 的方式能提高性能吗？

让我们看一看。

## 隐藏类

Javascript 是一种动态语言。Javascript 允许对象随时改变形状。变量和数据结构可以在任何时候包含不同类型的不同值。然而实际上，大多数程序都是相当静态的，浏览器试图利用这一点。

在 Chrome 中，每个对象文字和类都被视为一个形状。如果一个对象或类的形状改变了，比如一个属性被动态地添加或删除，Chrome 会看到新的形状，并尝试在它们之间转换。在某些情况下，如果 Chrome 很难找出某个东西的精确形状，它会把对象当作散列表(有点像 Elm 的`Dict`)。我们称这些形状为隐藏类。

当我们的 Javascript 代码似乎不需要同时处理许多不同的形状时，我们可以获得最佳的性能。幸运的是，Elm 是一种静态语言，所以这应该很常见。但是，在某些情况下，Elm 确实会产生不同的形状，这些形状可以作为相同的类型传递。我们来看一个例子:

```
type Maybe a
  = Just a
  | Nothing 
```

Enter fullscreen mode Exit fullscreen mode

Elm 的`Maybe`就是这么定义的。它被编译成下面的 Javascript(使用`--optimized` ):

```
var elm$core$Maybe$Just = function (a) {
    return {$: 0, a: a};
};

var elm$core$Maybe$Nothing = {$: 1}; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`Just`和`Nothing`的 Javascript 对象文字有不同的形状。因此，每个处理`Maybe`的 Javascript 代码都必须能够处理两种不同的形状。但是这代价高吗？

为了衡量效果，我做了两件事:

1)制作一个有望获得性能差异的基准，然后制作两个版本，其中一个是手工编码的，以避免开销。
2)看 Node 输出的汇编代码(Node 和 Chrome 用的是同一个 JS 引擎，V8)。

你可以在 [github](https://github.com/Skinney/elm-code-gen-experiments) 找到这个实验的代码。

我主要关注 Elm 的内置`List`类型，因为它在几乎每个程序中都有使用。这里的性能改进对每个使用 Elm 的人来说都是一大好处。

我们将关注以下基准:

```
benchmark "int +" <|
  \_ -> foldl (+) 0 intList 
```

Enter fullscreen mode Exit fullscreen mode

简单地对所有元素进行左折叠，将它们添加在一起。这里的性能变化将告诉我们迭代一个`List`有多快，理论上来说，消除处理多个隐藏类的任何开销应该会提高性能。

我们编译基准。翻看编译后的 JS 输出，可以找到这段代码:

```
var _List_Nil = { $: 0 };
function _List_Cons(hd, tl) { return { $: 1, a: hd, b: tl }; } 
```

Enter fullscreen mode Exit fullscreen mode

空的`List`看起来不同于一个`List`元素(如果你想知道`List`是如何工作的，我在 [Elm Europe 2017](https://www.youtube.com/watch?v=mmiNobpx7eI) 上解释得很好)。

我们复制 JS 文件，并做如下修改:

```
var _List_Nil = { $: 0, a: null, b: null };
function _List_Cons(hd, tl) { return { $: 1, a: hd, b: tl }; } 
```

Enter fullscreen mode Exit fullscreen mode

一个`List`应该不再是多态的(许多形状的)。

结果是:

*   Firefox，修改前:每秒 75，843 次操作
*   火狐，修改后:84，549 次操作/秒
*   Safari，修改前:248，531 次操作/秒
*   Safari，修改后:248，530 次操作/秒
*   Chrome，修改前:294，434 次操作/秒
*   修改后的 chrome:302，569 次操作/秒

因此，Safari 没有什么不同，但是 Chrome 和 Firefox 都有相当不错的改进:Firefox 提高了约 11%，Chrome 提高了约 4%。请记住，这是可以在编译器中实现的，不需要修改任何 Elm 代码，对于应用程序开发人员来说，这是一个不费力的性能改进。

我们还可以通过运行以下脚本来查看 V8 生成的代码:

```
node --print-opt-code --code-comments index.js > jit_log 
```

Enter fullscreen mode Exit fullscreen mode

通过读取没有修改的基准运行的`jit_log`，我们可以看到:

```
-------- Optimized code ---
optimization_id = 1
source_position = 48049
kind = OPTIMIZED_FUNCTION
name = $List$foldl
stack_slots = 10
compiler = turbofan
address = 0x28bd2bd6e9a1
Body (size = 2292)
Instructions (size = 2012)
<Assembly code> 
```

Enter fullscreen mode Exit fullscreen mode

而对于修改后的代码，我们看到

```
-------- Optimized code ---
optimization_id = 0
source_position = 48067
kind = OPTIMIZED_FUNCTION
name = $List$foldl
stack_slots = 10
compiler = turbofan
address = 0x2081135eec01
Body (size = 1848)
Instructions (size = 1600)
<Assembly code> 
```

Enter fullscreen mode Exit fullscreen mode

正如所料，当代码不必处理多态性时，它生成的代码更少。

然而，这两个日志中都有让我犹豫的东西:

```
Inlined functions (count = 1)
 0x3f2705632551 <SharedFunctionInfo A2> 
```

Enter fullscreen mode Exit fullscreen mode

日志的这一部分列出了内联了多少函数。在这种情况下，只有一个评估传入函数的函数被内联(我马上会解释这是什么意思)。这其实并不令人惊讶。`foldl`只包含一个函数调用，每个循环执行一次。这个函数通常也不会相同，所以它没有被内联是有意义的。但是，如果您查看所有其他已优化的函数，唯一被内联的函数要么是接受单个参数的函数(也称为 arity-1 函数)，要么是称为 A2、A3、A4 等的函数。

怎么回事？

## 内联

内联函数调用(用函数的实现替换函数调用)是编译器可以进行的最重要的优化之一。这不一定是因为函数调用非常昂贵，而是因为它允许编译器更好地理解代码做什么，并基于此执行优化。

让我们再来看看我们的基准:

```
benchmark "int +" <|
  \_ -> foldl (+) 0 intList 
```

Enter fullscreen mode Exit fullscreen mode

如果没有内联，这将调用`foldl`函数，这是一个为列表中的每个元素调用函数的循环。由于`foldl`可以接受任何函数，它会将中间数值存储为引用(即使它可以作为一个数字存储在堆栈中)，每次调用函数时在内存中执行查找。如果我们没有将 int 作为中间值存储，就像我们折叠其他东西一样，那么 Javscript 优化器可能会将所有值视为`foldl`中的通用散列表。

然而，使用内联，这个函数很可能被编译成一个 javascript 循环，根本不需要任何函数调用，并且只需要为循环中实际使用的类型编写专门的代码。这就像获得单态编译器的好处，而实际上没有单态编译器。

但是为什么很多函数没有被内联，这些 A2 都是什么东西？

## 逢迎

Elm 有这个 currying 的概念。给定以下功能:

```
add : Int -> Int -> Int
add a b =
  a + b 
```

Enter fullscreen mode Exit fullscreen mode

你可以像这样创建一个新函数:

```
add2 : Int -> Int
add2 =
  add 2 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你调用一个有足够参数的函数，它就会执行。如果你调用一个没有所有参数的函数，它会返回一个新的函数来接受缺少的参数。

上面的`add`函数就是这样编译成 JS:
的

```
function F(arity, fun, wrapper) {
  wrapper.a = arity;
  wrapper.f = fun;
  return wrapper;
}

function F2(fun) {
  return F(2, fun, function(a) { return function(b) { return fun(a,b); }; })

var author$project$Main$add = F2(function(a, b) {
  return a + b;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们的`add`函数，以及其他所有的 Elm 函数，都封装在一个对象中，该对象包含原始函数、函数期望的 arity 和一个 curried 函数。调用函数得用`A2`来完成，是这样实现的:

```
function A2(fun, a, b) {
  return fun.a === 2 ? fun.f(a, b) : fun(a)(b);
} 
```

Enter fullscreen mode Exit fullscreen mode

所以 A2 接受一个 F2 对象，如果所提供的函数实际上接受两个参数，则直接调用该函数，否则执行一个 curried 调用。

从 javascript 引擎的角度来看，这有一个很大的问题:除非我们进行整个程序分析(这太昂贵了),否则没有办法知道是否应该调用函数本身，或者是否应该使用 currying 来调用它。A2 调用本身可以内联，但仅此而已。

但是如果我们让 Elm 编译器变得更智能呢？如果 Elm 编译器知道一个函数需要多少个参数，它可以重写这个:

```
A2(author$project$Main$add, 1, 2) 
```

Enter fullscreen mode Exit fullscreen mode

进入

```
author$project$Main$add.f(1, 2) 
```

Enter fullscreen mode Exit fullscreen mode

我们制作了一个基准的副本，并为基准中的每个函数调用以及基准调用的函数手工进行这些更改。

这次我们将关注以下函数的结果:

```
benchmark "* 2" <|
  \_ -> map (\a -> a * 2) intList 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

*   Firefox，修改前:24，291 次操作/秒
*   火狐，修改后:50，927 次操作/秒
*   Safari，修改前:每秒 35，723 次操作
*   Safari，修改后:49，029 次操作/秒
*   修改前的 chrome:39，253 次操作/秒
*   修改后的 chrome:58，491 次操作/秒

非常好。Firefox 的性能提高了一倍，而 Chrome 和 Safari 的性能提高了约 30%。

当查看未修改代码的内联结果时:

```
Inlined functions (count = 1)
 0x13f84c332341 <SharedFunctionInfo A2> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到修改后发生了一些变化:

```
Inlined functions (count = 5)
 0x1f31bec396e1 <SharedFunctionInfo $map>
 0x1f31bec395a9 <SharedFunctionInfo $foldr>
 0x1f31bec39541 <SharedFunctionInfo $foldrHelper>
 0x1f31bec32049 <SharedFunctionInfo F2>
 0x1f31bec31fe1 <SharedFunctionInfo F> 
```

Enter fullscreen mode Exit fullscreen mode

然而，在查看生成的汇编代码时，我看到了许多包含以下内容的行:

```
call 0x1e5fad48abe0  (Call_ReceiverIsNotNullOrUndefined) 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用`someObject.f(args)`调用函数时，Chrome 必须确保`someObject`不是`null`或`undefined`。

我又运行了一次基准测试。这次我将函数放在了`F`包装器之外，并直接调用它们。

结果是:

*   Firefox，修改前:50，927 次操作/秒
*   火狐，修改后:59，632 次操作/秒
*   Safari，修改前:每秒 49，029 次操作
*   Safari，修改后:43，695 次操作/秒
*   Chrome，修改前:58，491 次操作/秒
*   修改后的 chrome:63，619 次操作/秒

Chrome 和 Firefox 的速度有所提升，Firefox 提升了 16 %, Chrome 提升了 8%。Safari 实际上看到了减速，但我不知道为什么。重新运行几次基准测试会得到完全不同的结果，我不知道该怎么理解。

## 结论

Elm 很快，但仍有变得更快的空间。通过改变 Elm 编译器输出 Javascript 的方式，我们可以显著提高 Elm 程序的性能。

## 进一步阅读

如果你想知道更多关于 Chrome 如何让 Javascript 变得更快，这是我遇到的两个最好的资源:

[单态性](https://mrale.ph/blog/2015/01/11/whats-up-with-monomorphism.html)
T3】V8 种类怎么了