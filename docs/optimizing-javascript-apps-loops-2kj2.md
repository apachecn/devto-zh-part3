# 如何使用循环优化你的 JavaScript 应用

> 原文：<https://dev.to/mihailgaberov/optimizing-javascript-apps-loops-2kj2>

最初发表于 [mihail-gaberov.eu](https://mihail-gaberov.eu) 。

[![ How to optimize your JavaScript apps using Loops](img/6e6c8264a65ad310770ce1c2f751ad78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L3qdteQT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/mihailgaberov/mihail-gaberov.eu/raw/master/src/pages/optimizing-javascript-apps-loops/optimizing-javascript-apps-loops.jpg)

为了提高 JavaScript 应用程序的性能，最容易也是最容易被忽略的一件事就是学习如何正确地编写高性能的循环语句。这篇文章背后的想法是帮助解决这个问题。

> 我们将看到 JavaScript 中使用的主要循环类型，以及如何以一种高性能的方式编写它们。

我们开始吧！

## 循环性能

当谈到循环性能时，争论总是关于使用哪个循环。哪个速度最快，性能最好？事实是，在 JavaScript 提供的四种循环类型中，只有一种明显比其他 for-in 循环慢。循环类型的选择应基于您的需求，而不是性能考虑。

有两个主要因素影响循环性能——每次迭代完成的工作量和迭代次数。
在下面的章节中，我们将了解如何通过降低这些参数来对环路性能产生积极的整体影响。

## 为循环

ECMA-262，第三版，定义了 JavaScript 的基本语法和行为的规范，定义了四种类型的循环。第一个是循环标准，它与其他类 C 语言共享语法:

```
for (var i = 0; i < 10; i++){
    //loop body
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能是最常用的 JavaScript 循环结构。为了理解我们如何优化它的工作，我们需要对它进行一点剖析。

### 解剖

for 循环由四部分组成:初始化、预测试条件、循环体、执行后。其工作方式如下——首先执行初始化代码(var I = 0；)，那么预测试条件(i < 10).如果预测试条件评估为真，则执行循环体，然后运行后执行代码(i++)。

### 优化

优化循环工作量的第一步是最小化对象成员和数组项查找的数量。

您还可以通过颠倒循环顺序来提高循环的性能。在 JavaScript 中，如果消除了额外的操作，反转循环确实会使循环的性能有所提高。

以上两种说法对其他两个更快的循环也有效(`while`和`do-while`)。

```
// original loop
for (var i = 0; i < items.length; i++){
    process(items[i]);
}

// minimizing property lookups
for (var i = 0, len = items.length; i < len; i++){
    process(items[i]);
}

// minimizing property lookups and reversing
for (var i = items.length; i--; ){
    process(items[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

## While 循环

第二种类型的循环是 while 循环。这是一个简单的预测试循环，由预测试条件和循环体组成。

```
var i = 0;
while(i < 10){
    //loop body
    i++;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 解剖

如果预测试条件评估为真，则执行循环体。如果不是-它被跳过。每个`while`循环都可以替换为 for，反之亦然。

### 优化

```
// original loop
var j = 0;
while (j < items.length){
    process(items[j++]);
}

// minimizing property lookups
var j = 0,
    count = items.length;
while (j < count){
    process(items[j++]);
}

// minimizing property lookups and reversing
var j = items.length;
while (j--){
    process(items[j]);
} 
```

Enter fullscreen mode Exit fullscreen mode

## Do-While 循环

`do-while`是第三种循环，也是 JavaScript 中唯一的测试后循环。由体循环和后测试条件组成:

```
var i = 0;
do {
    //loop body
} while (i++ < 10); 
```

Enter fullscreen mode Exit fullscreen mode

### 解剖

在这种类型的循环中，循环体总是至少执行一次，然后评估后测试条件，如果为真，则执行另一个循环。

### 优化

```
// original loop
var k = 0;
do {
    process(items[k++]);
} while (k < items.length);

// minimizing property lookups
var k = 0,
    num = items.length;
do {
    process(items[k++]);
} while (k < num);

// minimizing property lookups and reversing
var k = items.length - 1;
do {
    process(items[k]);
} while (k--); 
```

Enter fullscreen mode Exit fullscreen mode

## For-In 循环

第四种也是最后一种循环称为`for-in`循环。它有非常特殊的用途- **枚举任何 JavaScript 对象的命名属性**。下面是它的样子:

```
for (var prop in object){
    //loop body
} 
```

Enter fullscreen mode Exit fullscreen mode

## 解剖

它类似于常规的`for`循环，只是名字不同。它的工作方式完全不同。这种差异使得它比其他三个循环慢得多，其他三个循环具有相同的性能特征，因此试图确定哪个最快是没有用的。每次执行循环时，变量 prop 都有对象上另一个属性的名称，它是一个字符串。它将一直执行，直到返回所有属性。这些将是对象本身的属性，以及通过其原型链继承的属性。

### 备注

> 永远不要使用“for-in”来迭代数组的成员。

这个循环的每次迭代都会导致在实例或原型上进行属性查找，这使得 for-in 循环比其他循环慢得多。对于相同的迭代次数，它可能比其他的慢七倍。

## 结论

👍`for`、`while`和`do-while`环路都具有相似的性能特征，因此没有一种环路类型明显快于或慢于其他类型。

👍避免`for-in`循环，除非你需要迭代许多未知的对象属性。

👍提高循环性能的最佳方法是减少每次迭代完成的工作量，并减少循环迭代的次数。

我希望这对你有用，就像对我一样！

🔥感谢阅读！🔥

## 资源

[“高性能 JavaScript”——尼古拉斯·c·扎卡斯](https://www.amazon.com/High-Performance-JavaScript-Application-Interfaces/dp/059680279X)