# 揭秘长箭“算符”

> 原文：<https://dev.to/somedood/demystifying-the-long-arrow-operator-4711>

我最近偶然发现了一些我觉得非常有趣的代码。它本质上迭代了一个循环*，但是有一个转折*。它使用了神秘的**长箭【符】**。

```
const pets = ['Lucky', 'Sparkles', 'Presto', 'Fluffykins'];
let i = pets.length;

// A wild LONG ARROW OPERATOR appeared!
while (i --> 0) {
  console.log(pets[i]);
}

// 'Fluffykins'
// 'Presto'
// 'Sparkles'
// 'Lucky' 
```

Enter fullscreen mode Exit fullscreen mode

这个“操作符”的有趣和不寻常之处在于，它反向遍历`pets`数组，如控制台输出所示。似乎写`i --> 0`就像在倒数`0`。瞧，它实际上正在引擎盖下倒计时。

# “符”背后的魔力

到目前为止，在本文中,“operator”一词周围使用引号并非巧合。可以说，长箭头“操作符”并不是真正的*操作符。更准确地说，它是两个操作符的组合*。

长箭头“运算符”(`-->`)只是由[后缀减量运算符(`--` )](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Decrement_(--)) 和[大于运算符(`>` )](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Greater_than_operator) 组合而成。

由于 JavaScript 在大多数时候会忽略空白，我们可以巧妙地将代码格式化，将`--`和`>`粘合成`-->`。我们可以写`x --> 0`，而不是说`x-- > 0`。不管格式如何，JavaScript 都会将长箭头“操作符”解释为两个独立的操作符。

> **注意:**如果说`x--`可以用于数字比较似乎有些奇怪，我最近写了一篇关于增量和减量操作符的细微差别的文章。你可以在这里读到它[。](https://dev.to/somedood/the-difference-between-x-and-x-44dl)

```
// All of these _output_ the same thing to the console.
// EXAMPLE 1: Good ol' `while` loops
let a = 5;
while (a > 0) {
  a--;
  console.log(a);
}

// EXAMPLE 2: Good ol' `for` loops
for (let b = 4; b >= 0; b--) {
  console.log(b);
}

// EXAMPLE 3: Combination of two operators
let c = 5;
while (c-- > 0) {
  console.log(c);
}

// EXAMPLE 4: Long arrow "operator"
let d = 5;
while (d --> 0) {
  console.log(d);
} 
```

Enter fullscreen mode Exit fullscreen mode

# 不要放弃循环

所以你有它。神秘的长箭头“运算符”只是两个运算符的组合。我认为这是一种非常好的阅读代码的方式，因为它与微积分中的极限符号非常相似。

也就是说，这里列出了我阅读`x --> 0`的许多方式。

*   "随着`x`接近`0`"
*   "`x`到`0`"
*   "倒计时`x`到`0`"
*   "递减`x`直到它达到`0`"
*   "从`x`中减去`1`，直到到达`0`"

虽然长箭头“操作符”看起来很好读，但我不会用它来写我的代码。代码格式太聪明了。乍一看，特别是对于一个语言新手来说，这似乎一点也不直观。人们可以在 Google 上快速搜索 JavaScript、Java 或 C++中的一些长箭头“操作符”,结果发现关于它的资源并不多。

只是不够“初学者友好”，这就是我不喜欢它的原因。人们必须注意后缀减量运算符的[返回值，才能完全理解为什么这样的代码在语法上是正确的。初学者不应该为学习一门复杂的编程语言而烦恼。*明的比暗的好，俗话说*。](https://dev.to/somedood/the-difference-between-x-and-x-44dl)

除此之外，长箭头“operator”的作用类似于倒计时。因此，它会反向迭代数组，这在某些情况下可能不是理想的行为。

总而言之，长箭头“operator”是格式化两个不同操作符的一种巧妙方法。除非你(和你的同行)完全习惯阅读长箭头符号~~，或者你只是想用一些他们从未见过的奇怪语法~~给你的朋友留下深刻印象，否则最好坚持使用`for`循环进行通用迭代。