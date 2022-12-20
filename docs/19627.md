# JavaScript (ES5)循环

> 原文：<https://dev.to/martyhimmel/javascript-es5-loops-289o>

*此文最初于 2016 年 12 月 26 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

## `for`循环往复

循环可能是最常见的循环类型。`for`循环的两个常见用途是遍历一个数组并对每个元素做一些事情，以及遍历一个数组以找到一组匹配的值。结构是这样的:

```
for (initial expression; condition; incrementing expression) {
  // do something on each pass
} 
```

Enter fullscreen mode Exit fullscreen mode

“初始表达式”是标记循环起点的表达式。“条件”是循环的终点。只要条件为真，循环就会继续。一旦条件为假，循环终止。“递增表达式”是指初始表达式在循环的每一次循环中是如何变化的(注意:它实际上不必递增——它可以递减，递增 2，等等。).

这里有几个`for`循环的例子。第一个循环是最常见的 for 循环，它从数组的第 0 个元素(开始)开始计数。

```
// This will be used in all the examples.
var numbersAsWords = ['one', 'two', 'three'];

for (var i = 0; i < numbersAsWords.length; i++) {
  console.log(numbersAsWords[i]);
}
// one
// two
// three 
```

Enter fullscreen mode Exit fullscreen mode

这个循环从数组的最后一个元素开始倒数。

```
for (var i = numbersAsWords.length - 1; i >= 0; i--) {
  console.log(numbersAsWords[i]);
}
// three
// two
// one 
```

Enter fullscreen mode Exit fullscreen mode

当运行一个`for`循环时，每次循环都会计算“条件”部分的任何表达式。当遍历大量元素时，可能会导致性能问题。使用前面的两个例子，如果 numbersAsWords 数组有 100，000 个元素，那么该数组的长度将被计算 100，000 次(每一次)。一种解决方法是在循环的“初始表达式”部分创建一个额外的变量，并存储数组的长度。那么数组长度只在循环开始时计算一次，而不是每次循环都计算。这里有一个例子:

```
for (var i = 0, arrLength = numbersAsWords.length; i < arrLength; i++) {
  console.log(numbersAsWords[i]);
}
// one
// two
// three 
```

Enter fullscreen mode Exit fullscreen mode

## `for…in`循环往复

`for…in`循环用于循环对象的属性。下面是结构:

```
for (var prop in obj) {
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

循环语句可以理解为“针对对象中的每个属性”当使用`for…in`循环时，您可以访问每个属性和值，而无需知道属性的具体名称。记住，对象是键/值对的集合(`"property": "value”`)。可以直接用`prop`(用结构例子)得到属性名，用括号符号- `obj[prop]`得到值。这里有一个例子:

```
var myWidget = {
  company: 'ACME',
  unitsSold: 42000,
  isModular: true
};

for (var prop in myWidget) {
  // log each property of the object as "property: value"
  console.log(prop + ': ' + myWidget[prop]);
}
// company: ACME
// unitsSold: 42000
// isModular: true 
```

Enter fullscreen mode Exit fullscreen mode

## `while`循环往复

`while`循环继续运行，直到条件为假。结构为:

```
while (condition) {
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

循环非常适合迭代一个集合(例如一个数组)。`while`循环也可以以同样的方式使用，但通常更适合做一些事情，直到用户输入或某种外部条件要求停止。

等待用户输入的一个例子是“游戏循环”每一款游戏都是围绕游戏循环来构建的。一个例子是当玩家在玩的时候，继续运行游戏——这是最基本的游戏循环，按下“退出”按钮就会终止。另一个例子是当角色的生命值高于零时，继续战斗。俄罗斯方块的一个例子是，当方块在顶线以下时，将下一个方块送到场地。这个清单可以无限期地继续下去。

这里有一个`for`循环变体的例子:

```
var i = 1;
while (i <= 3) {
  console.log('Line ' + i);
  i++;
}
// Line 1
// Line 2
// Line 3 
```

Enter fullscreen mode Exit fullscreen mode

这是一个游戏循环的例子:

```
var isPlaying = true;
while (isPlaying) {
  nextAction();
} 
```

Enter fullscreen mode Exit fullscreen mode

***警告:使用`while`(和`do…while`)循环很容易产生无限循环。确保你已经准备好终止循环的东西(如果循环一个数字变量，一个递增器，用户输入的停止选项，等等)。).***

## `do…while`循环往复

循环会做一次，然后继续运行，直到一个条件为假。该结构与`while`循环略有不同，因为代码包含在`do`块中，而`while`只包含条件。

```
do {
  // do something
} while (condition) 
```

Enter fullscreen mode Exit fullscreen mode

一个`do…while`循环保证至少运行一次，因为它在检查条件之前运行`do`代码块。在第一次运行后，只要条件为真，它将继续在`do`块上循环。

由于`do`块是在没有检查条件的情况下首先运行的，如果不小心的话，这可能会导致意想不到的问题。这里有两个`do…while`循环的例子。这个工作正常:

```
var myArray = ['John', 'Jane'];
var i = 0;
do {
  console.log(myArray[i]);
  i++;
} while (i < myArray.length);
// John
// Jane 
```

Enter fullscreen mode Exit fullscreen mode

这一个无声的失败:

```
var myArray = [];
var i = 0;
do {
  console.log(myArray[i]);
  i++;
} while (i < myArray.length);
// undefined 
```

Enter fullscreen mode Exit fullscreen mode

第二个例子有一些隐患。在这一点上，它将`undefined`记录到控制台，没有任何东西损坏。但是，如果您需要操作数组中的数据，就会破坏脚本。下面是第二个例子的修改版本:

```
var myArray = [];
var i = 0;
do {
  console.log(myArray[i].toUpperCase());
  i++;
} while (i < myArray.length);
// Uncaught TypeError: Cannot read property 'toUpperCase' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

在调用未定义的数组元素时添加`.toUpperCase()`(一个字符串方法)会破坏脚本。出于这个原因，除非有充分的理由，否则最好不要使用`do…while`循环。通常情况下，你可以用一个`while`循环完成同样的事情。