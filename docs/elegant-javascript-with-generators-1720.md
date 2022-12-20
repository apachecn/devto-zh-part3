# 使用生成器在 JavaScript 中优雅地迭代

> 原文：<https://dev.to/manlycoffee/elegant-javascript-with-generators-1720>

过去，JavaScript 中的迭代经常涉及 while 循环、for 循环和递归。最终，程序员设计出了用于迭代的模式。一种这样的模式是[迭代器模式](https://en.wikipedia.org/wiki/Iterator_pattern)。

这是一种如此强大而优雅的模式，以至于它成为了 JavaScript 编程语言的核心部分。

在本文中，我将介绍生成器、可迭代对象和迭代器，以及如何应用它们从数据结构中检索数据。

## 发电机引物

生成器是*生成*一系列值，或者*运行*一系列操作的一种方式。这个系列要么最终停止，要么永远继续下去。

这就是你如何编写一个生成器:

```
function * myGenerator() {
  yield 1;
  yield 2;
  yield 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

与函数不同，当你调用`myGenerator`时，你*不会立刻得到`1`、`2`和`3`。相反，你得到的是所谓的 *iterable* (实际上，它是一个 iterable-iterator。稍后将详细介绍)。Iterables 是 JavaScript 语言的核心。*

为了提取这些值，您需要通过*可迭代的*来*迭代*。你可以通过`for-of`循环来完成。

```
const iterable = myGenerator();

for (const value of iterable) {
  console.log(value);
}

// Should give us:
// 1
// 2
// 3 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果你想把 iterable 变成一个数组，你不需要使用`for-of`；相反，你可以只把它“散布”成一个数组。

```
const iterable = myGenerator();

const fromIterable = [...iterable]; 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 中 iterables 的多功能性是这种模式如此强大的原因。事实上，JavaScript 中的许多构造要么接受可迭代的，要么本身就是可迭代的！例如，数组被定义为可迭代的。

如果您愿意，您可以将 iterable“扩展”到一个参数列表中。

```
someSpreadable(...iterable); 
```

Enter fullscreen mode Exit fullscreen mode

数组并不排斥函数展开运算符； *iterables* 通常可以应用 spread 运算符。

使用生成器，您不仅可以“产生”单个值，还可以“产生”包含在 iterable 中的各个*值。因此，您可以重写上面的`myGenerator`函数来“产生”单个的`1`、`2`和`3`，但不是从一个数组中产生。只是一定要在`yield`关键字后面加上一个`*`。* 

```
function * myGenerator() {
  yield * [1, 2, 3];
} 
```

Enter fullscreen mode Exit fullscreen mode

### 无穷级数

如果你想生成一个无穷级数，你可以创建一个生成器来实现。这将涉及 while 循环，但是一旦完成，您就可以应用您需要的任何助手来提取必要的值。让我们生成斐波那契数列。

```
function * fibonacci() {
  let previous = 0;
  let i = 1;
  while (true) {
    previous = i + previous;
    yield previous;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了得到序列的前十个元素，我们可以写一个生成器。

```
function * take(iterable, n) {
  let i = 0;
  for (let value of iterable) {
    yield value;
    i++;
    if (i >= n) { break; }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们可以得到斐波那契数列的前十个值。

```
const iterator = take(fibonacci(), 10);
console.log([...iterator]);
// -> [1, 1, 2, 3, 5, 8, 13, 21, 34, 55] 
```

Enter fullscreen mode Exit fullscreen mode

一般不会重新发明轮子。上面的`take`实现已经存在于 [IxJS 库](https://github.com/ReactiveX/IxJS)中。或许，在未来，甚至会有内置于 JavaScript 中的助手函数[。](https://github.com/tc39/proposal-iterator-helpers/issues/8)

## 迭代器和迭代器

在上一节中，我们讨论了生成器。生成器是返回可迭代的函数。Iterables 是具有由`Symbol.iterator`键控的方法的对象。该方法的存在向各种 JavaScript 结构发出了对象是可迭代的信号。`Symbol.iterator`方法返回一个*迭代器*。迭代器对象实现了一个`next`方法，该方法本身返回一个具有属性`value`和`done`的对象。

属性`value`表示当前迭代中的值；`done`是一个布尔值，表示迭代是否完成。

下面是一个 iterable 对象的示例实现，它永远返回一系列数字`1`。

```
const someIterable = {
  [Symbol.iterator]() {
    return {
      next() {
        return { value: 1, done: false }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上一节关于生成器的内容中，提到了生成器返回一个 iterable。然而，这并不完全正确。它们实际上返回了一个“可迭代的迭代器”。也就是说，它们既是可迭代的，也是迭代器。因此，我们可以使用一个生成器来定义上面的`Symbol.iterator`方法。

下面是使用生成器的实现。

```
const someIterable = {
  *[Symbol.iterator]() {
    while (true) {
      yield 1;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

两种实现几乎完全相同。

## 数据结构

如果需要高效地存储和检索数据，可以使用树状结构。但是，如果需要遍历这些值，就需要遍历树。

发电机可以促进这一点。我们将使用一个[https://youtu.be/qHCELlYY08w?t=22](https://en.wikipedia.org/wiki/Binary_search_tree)来演示这一点(这里是这个 T2 的动画)。

树形数据结构有节点。我们通过节点来遍历整棵树。生成器可以促进递归下降，因此，我们可以让节点本身成为可迭代的！因此，左右节点都是可迭代的(因为它们分别表示左右子树)；我们可以“屈服”他们的价值观。

```
class Node {
  // ... let's ignore the implementation of `Node`

  *[Symbol.iterator]() {
    if (this.left !== null) { yield * this.left; }
    yield this.value;
    if (this.right !== null) { yield * this.right; }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，二叉查找树本身也可以“让出”根节点。

```
class BinarySearchTree {
  // ... let's ignore the implementation of the tree

  *[Symbol.iterator]() {
    if (this.root !== null) { yield * this.root; }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以这样使用二叉查找树:

```
const tree = new BinarySearchTree();

tree.insert(10, 'bar');
tree.insert(3, 'foo');
tree.insert(11, 'baz');

console.log([...tree]);
// -> [ 'foo', 'bar', 'baz' ] 
```

Enter fullscreen mode Exit fullscreen mode

## 其他可重复项的例子

就可迭代对象而言，已经提到过生成器返回可迭代对象，数组是可迭代对象，上面的二叉查找树是自定义可迭代对象的一个例子。JavaScript 有另外两个已定义的可迭代结构，分别是 [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 和 [`Set`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

我们可以使用 Map 或 Set，并以与其他可迭代对象相同的方式与它们交互。

## 结论

Iterables 是 JavaScript 的核心特性。它们是一种生成值的方法，您可以逐个迭代。它们是公开对象的底层值集的一种表达方式。因为它们是 JavaScript 的核心，所以很多语言结构都大量使用它们，未来的 JavaScript 修订版将继续使用 iterables，可能会采用新的语法。

因此，与其依赖数组来表示集合，不如考虑定义一个兼做 iterable 的对象。这样，你不仅给了代码的用户更多的权力，而且通过只给出用户代码所要求的，并且在被要求时只给出*来节省计算。*