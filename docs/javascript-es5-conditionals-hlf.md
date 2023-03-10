# JavaScript (ES5)条件句

> 原文：<https://dev.to/martyhimmel/javascript-es5-conditionals-hlf>

*此文最初于 2016 年 12 月 27 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

条件允许用户交互/选择，用于控制程序的流程。想想你一天中所做的所有选择，以及这些选择的结果。这就是程序中条件句的基本工作方式——当你做一个动作时，程序向一个方向流动；如果选择不同的操作，程序会以另一种方式运行。

这里有一个生活中条件句的例子。当闹钟响起时，你可以打个盹或者起床。这个选择是一个分支点——如果你打盹，那么你会睡得更多，但没有时间吃早餐。如果你起床，你有时间准备和吃早餐。如你所见，这是一个非常简单的例子。在闹钟响起和吃早餐之间，早晨的例行公事有更多的选择。一路上的每一个选择都可以被认为是有条件的——选择和选择的结果。

## `if`报表

一个`if`语句是最基本的条件类型。它对条件作出反应——如果条件为真，则运行语句中的任何内容。如果条件为假，则跳过该语句。结构如下:

```
if (condition) {
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是`if`语句的一些常见用法:

*   检查值是否属于某种类型
*   将数值与期望值进行比较
*   将字符串变量与显式预期的字符串进行匹配

```
var myString = 'This is a string';
if (typeof myString === 'string') {
  console.log(myString);
}
if (typeof myString === 'number') {
  console.log('Number: ' + myString);
}
// This is a string 
```

Enter fullscreen mode Exit fullscreen mode

```
var myNumber = 42;
if (myNumber > 0) {
  console.log('Greater than 0');
}
if (myNumber > 100) {
  console.log('Greater than 100');
}
if (myNumber === 42) {
  console.log('The answer to everything');
}
// Greater than 0
// The answer to everything 
```

Enter fullscreen mode Exit fullscreen mode

```
var color = 'blue';
if (color === 'blue') {
  console.log('The sky is blue');
}
if (color === 'green') {
  console.log('The grass is green');
}
// The sky is blue 
```

Enter fullscreen mode Exit fullscreen mode

正如您在这些示例中看到的，对于为真的条件，执行语句，而跳过条件为假的语句。

## `if, else`报表

语句可以被认为是一个分支点。如果`if`条件为真，则执行该块中的代码，跳过`else`块。如果`if`条件为假，则跳过该块并执行`else`块。结构如下:

```
if (condition) {
  // do something
} else {
  // default action
} 
```

Enter fullscreen mode Exit fullscreen mode

`else`块充当默认值——只要`if`条件为假，代码就会执行。这里有几个例子。

```
var choice = 'left';
if (choice === 'left') {
  console.log('You have chosen the left path.');
} else {
  console.log('You have chosen the right path.');
}
// You have chosen the left path. 
```

Enter fullscreen mode Exit fullscreen mode

```
var choice = 'right';
if (choice === 'left') {
  console.log('You have chosen the left path.');
} else {
  console.log('You have chosen the right path.');
}
// You have chosen the right path. 
```

Enter fullscreen mode Exit fullscreen mode

## `if, else if, else`报表

`if, else if, else`语句与`if, else`语句相同，但有更多分支。您可以拥有任意多的`else if`声明。结构如下:

```
if (condition) {
  // do something
} else if (different condition) {
  // do something else
} else {
  // default action
} 
```

Enter fullscreen mode Exit fullscreen mode

`else`语句仍然是默认的，但是首先要检查更多的条件。让我们对上面的例子进行扩展。

```
var choice = 'right';
if (choice === 'left') {
  console.log('You have chosen the left path.');
} else if (choice === 'right') {
  console.log('You have chosen the right path.');
} else {
  console.log('That is an invalid choice.');
}
// You have chosen the right path. 
```

Enter fullscreen mode Exit fullscreen mode

```
var choice = 42;
if (choice === 'left') {
  console.log('You have chosen the left path.');
} else if (choice === 'right') {
  console.log('You have chosen the right path.');
} else {
  console.log('That is an invalid choice.');
}
// That is an invalid choice. 
```

Enter fullscreen mode Exit fullscreen mode

类似于单个`if`语句，您可以在这里省略`else`语句。如果所有的`if`和`else if`条件都为假，那么什么都不会发生，它跳过整个集合。

```
var time = 1200;
var timeOfDay = 'It is noon.';
if (time < 1200) {
  timeOfDay = 'Sometime in the morning.';
} else if (time > 1200) {
  timeOfDay = 'Sometime in the afternoon or evening.';
}
console.log(timeOfDay);
// It is noon. 
```

Enter fullscreen mode Exit fullscreen mode

## `switch`报表

`switch`报表(又称`switch, case`报表)类似于`if, else if, else`报表。结构是这样的。

```
switch (expression) {
  case 'one':
    // 'one' matches the result of the expression, do this
    break;
  case 'two':
    // 'two' matches the result of the expression, do this
    break;
  default:
    // no matching cases, do this
    break;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`switch`行检查表达式，类似于`if`条件。一旦表达式被求值，它就遍历`case`值列表，直到找到匹配，然后执行`case`语句中的代码。如果没有找到匹配，则执行`default`语句(类似于`else`语句)。

像`else if`语句一样，您可以拥有任意多的`case`块。类似地，`default`块也是可选的(就像`else`语句一样)。

语句通常用于将用户输入与一组预期值进行匹配。这里有一个例子。

```
var color = 'blue';
switch (color) {
  case 'blue':
    console.log('You picked blue');
    break;
  case 'red':
    console.log('You picked red');
    break;
  case 'yellow':
    console.log('You picked yellow');
    break;
  default:
    console.log('Invalid color');
    break;
}
// You picked blue 
```

Enter fullscreen mode Exit fullscreen mode

每个案例末尾的`break;`行很重要。如果没有这些，失败就会发生。这意味着当一个案例的代码运行并且没有中断时，该代码会转到下一个案例，并且该代码块也会执行。避免这种情况的一个好的做法是编写 case 值，然后在添加代码之前立即添加 break。

也就是说，有时失败效应是你希望发生的。在下面的例子中，我们检查月份以获得季节。

```
var month = 'April';
switch (month) {
  case 'December':
  case 'January':
  case 'February':
    console.log('Winter');
    break;
  case 'March':
  case 'April':
  case 'May':
    console.log('Spring');
    break;
  case 'June':
  case 'July':
  case 'August':
    console.log('Summer');
    break;
  case 'September':
  case 'October':
  case 'November':
    console.log('Fall');
    break;
  default:
    console.log('Not a valid month');
    break;
}
// Spring 
```

Enter fullscreen mode Exit fullscreen mode