# 函数式编程简介

> 原文：<https://dev.to/ganderzz/introduction-to-functional-programming-598e>

这篇文章来自我在 Boise Code Camp 2019 上展示的一个例子，比较了解决问题的命令式和功能式方法。目标不是完整地教授函数式编程，而是引入一种不同于传统方法的思维方式(循环、突变等)。当面临一个问题时，拥有不同的参照系会给工具箱增加更多的工具。

函数式编程的基础可以用三个主要概念来表示:

*   不可变数据结构
*   纯函数
*   一级函数

让我们快速了解一下这些项目符号的含义。

### 不可变的数据结构

当使用像 JavaScript 这样的编程语言时，我们可以将数据赋给变量`let myVariable = 5;`。但是，没有什么能阻止我们在稍后的`myVariable = "Now I'm a string."`中将变量重新赋值给其他的东西。这可能是危险的——也许另一个函数依赖`myVariable`作为一个数字，或者如果一些异步函数同时在`myVariable`上工作呢！我们可能会遇到合并冲突。

##### 举例

```
const obj = {
  a: 1,
  b: 2
};

function addOne(input) {
  return {
    a: input.a + 1,
    b: input.b + 1
  };
}

const newObj = addOne(obj);

newObj === obj; // false 
```

Enter fullscreen mode Exit fullscreen mode

### 纯函数

纯函数没有副作用。那是什么意思？嗯，一个仅仅根据输入来计算输出的函数可以被认为是一个纯函数。如果我们的函数接受一个输入，执行数据库更新，然后返回一个值，那么我们在代码中包含了一个副作用——更新数据库。多次调用该函数可能并不总是返回相同的结果(内存不足、数据库被锁定等)。拥有纯函数对于帮助我们编写无错误、易于测试的代码至关重要。

##### 举例

```
function notPureAdd(a, b) {
  return a + new Date().getMilliseconds();
}

function pureAdd(a, b) {
  return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第一类函数

术语“第一类”可能看起来很奇怪，但它的意思是函数可以像使用其他数据类型一样被传递和使用。例如字符串、整型、浮点型等。支持一级函数的编程语言允许我们将函数传递给其他函数。把这想象成依赖注入。如果你使用过 JavaScript，那么第一类函数在任何地方都会用到，在接下来的例子中我们会更多地接触到它们。

##### 举例

```
// robot expects a function to be passed in
function robot(voiceBox) {
  return voiceBox("bzzzz");
}

// console.log is a function that logs to the console
robot(console.log);
// alert is a function that shows a dialog box
robot(alert); 
```

Enter fullscreen mode Exit fullscreen mode

### 比较命令式和函数式编程

为了展示命令式编程和函数式编程之间的基本比较，让我们将数组`[1, 2, 3, 4]`中的数字相加，并获得其总和。

强制性地，我们可以这样写:

```
const list = [1, 2, 3, 4];

let sum = 0;

for (let i = 0; i < list.length; i++) {
  sum += list[i];
}

console.log(sum); // 10 
```

Enter fullscreen mode Exit fullscreen mode

将它转换成功能风格，我们有一个大问题。我们对`sum`进行变异，使其在列表的每次迭代中都有不同的值。纪念...不可变的数据结构。

为了让这段代码发挥作用，让我们来分解一下求和是如何计算的。

首先，我们从某个值开始，在我们的例子中是`0`(见第`let sum = 0;`行)！接下来，我们取出数组`1`中的第一项，并将其添加到我们的 sum 中。现在我们有了`0 + 1 = 1`。然后我们重复这个步骤，取出`2`并将其加到总和`1 + 2 = 3`中。这样继续下去，直到我们遍历了数组的长度。

用另一种方式来想象这个:

```
0 + [1,2,3,4]
0 + 1 + [2,3,4]
1 + 2 + [3,4]
3 + 3 + [4]
6 + 4
10 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把这个算法想成两个独立的函数，首先我们需要某种方式把数字加在一起。

```
function add(a, b) {
  return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

*简单！*

接下来，我们需要某种方法来循环遍历给我们的数组。由于大多数函数式编程通常依赖于递归而不是循环，我们将创建一个递归函数来循环遍历我们的数组。让我们看看这可能是什么样子。

```
function loop(list, index = 0) {
  if (!list || index > list.length - 1) {
    // We're at the end of the list
    return;
  }

  return loop(list, index + 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数中，我们得到了一个我们想要遍历的列表，以及一个用来确定我们当前在列表中的位置的索引。如果我们到达了列表的末尾，或者给出了一个无效的列表，那么我们就完成了循环。如果没有，我们将再次调用`loop`，递增索引。尝试在循环函数中添加一个`console.log(list[index])`，在`return loop(list, index + 1);`之前！我们应该看到`1 2 3 4`打印到控制台上！

为了最终对数组求和，我们需要结合我们的`loop`和`add`函数。在这个例子中，请记住我们上面的算法:

```
function loop(list, accu = 0, index = 0) {
  if (!list || index > list.length - 1) {
    return accu;
  }

  const result = add(accu, list[index]);

  return loop(list, result, index + 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们重新安排了`loop`函数中的一些参数。现在我们有了一个`accu`参数(accumulation ),它将跟踪列表中给定位置的总和。我们还直接使用我们的`add`函数来获得添加了列表中当前项目的`accu`的结果。如果我们`console.log(loop(list));`我们应该把结果`10`打印到控制台上！

不如我们更进一步？如果我们不想对数字列表求和，而是将它们相乘，会怎么样？目前我们不得不复制我们的`loop`函数，粘贴它，并把`add`改成别的东西(`multiply`也许？).多痛苦啊！还记得第一类函数吗？我们可以用这个想法来让我们的代码更加通用。

```
function loop(func, list, accu = 0, index = 0) {
  if (!list || index > list.length - 1) {
    return accu;
  }

  const result = func(accu, list[index]);

  return loop(func, list, result, index + 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，唯一改变的是我们现在向接受函数的`loop`添加了一个新参数。我们将调用传入的函数来获得结果，而不是调用`add`。现在我们可以很容易地将`add`、`multiply`、`subtract`等列入我们的名单。

*   `loop(add, list);`
*   `loop(function(a, b) { return a * b; }, list);`

我们不再只是循环遍历数组，而是像折叠纸一样折叠数组，直到得到一个结果。在函数式编程中，这个函数可能被称为`fold`，在 Javascript 中，我们将它称为`reduce`！

```
function reduce(func, list, accu = 0, index = 0) {
  if (!list || index > list.length - 1) {
    return accu;
  }

  const result = func(accu, list[index]);

  return reduce(func, list, result, index + 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结束

我们看了函数式编程的基础，以及分解一个问题如何能给我们相同问题不同的解决方案。`reduce`被视为其他操作的基础，如`map()`或`filter()`。这是我给你的测试，我们如何只用我们刚刚创建的`reduce()`来实现这两个函数？

*印度语*

还记得 reduce 的算法吗？

```
0 + [1,2,3,4]
0 + 1 + [2,3,4]
1 + 2 + [3,4]
3 + 3 + [4]
6 + 4
10 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不是从`0`开始，而是从`[]`数组开始，会怎么样？

[回答](https://jsbin.com/femoburako/1/edit?js,console)