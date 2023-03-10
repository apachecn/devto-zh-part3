# JavaScript 开关案例介绍

> 原文：<https://dev.to/bolajiayodeji/introduction-to-javascript-switch-cases-4mp4>

在这篇短文中，我将向您介绍 JavaScript 开关案例，以及如何通过实际例子使用它们。

*PS:这篇文章不是为那些没有任何 JavaScript 知识的人写的，它是
期望你正在上一些教程或课程，并且你刚刚到达
切换案例部分。*

本文将通过更多的实际例子来更好地解释，以帮助您
深入理解开关案例。

### 先决条件。

*   基本的 JavaScript 知识
*   代码编辑器
*   网络浏览器
*   你的大脑:)

一个`switch`语句基本可以代替 JavaScript 中的多个`if`检查。

它提供了一种更具描述性的方法来比较一个值与多个变量。

### **开关语法**

`switch`有一个或多个`case`模块和一个可选的默认情况。

```
switch(x) {
  case 'value1':  // if (x === 'value1')
    //code here
    [break]

  case 'value2':  // if (x === 'value2')
    //code here
    [break]

  default:
    //code here
    [break]
} 
```

Enter fullscreen mode Exit fullscreen mode

*   检查`x`的值是否严格等于从第一个`case`(即`value1`)到第二个`value2`的值，依此类推。
*   如果发现相等，`switch`从相应的`case`开始执行代码，直到最近的`break`(或直到`switch`结束)。
*   如果没有匹配的案例，则执行`default`代码(如果存在)。

### **几个真实的例子**

*   **简单播放&暂停开关**

`switch`语句可用于基于数字或
字符串的多个分支:

```
switch (movie) {
  case 'play':
    playMovie();
    break;
  case 'pause':
    pauseMovie();
    break;
  default:
    doNothing();
} 
```

Enter fullscreen mode Exit fullscreen mode

如果不添加一个`break`语句，执行将“失败”到下一级
。如果你真的想帮助调试的话，有必要故意用
注释来标记这个失败:

```
switch (movie) {
  case 'play': // fallthrough
  case 'pause':
    pauseMovie();
    break;
  default:
    doNothing();
} 
```

Enter fullscreen mode Exit fullscreen mode

默认子句是可选的。如果你愿意，你可以在开关部分
和案例中都有表达式；使用
`===`运算符:
在两者之间进行比较

```
switch (3 + 7) {
  case 5 + 5:
    correct();
    break;
  default:
    neverhappens();
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **简单数学计算开关**

```
let average = 2 + 6;

switch (average) {
  case 4:
    alert( 'Too small' );
    break;
  case 8:
    alert( 'Exactly!' );
    break;
  case 10:
    alert( 'Too large' );
    break;
  default:
    alert( "Incorrect values!" );
} 
```

Enter fullscreen mode Exit fullscreen mode

这里`switch`从第一个`case`变体开始比较`average`，即
是`4`。匹配失败。

然后`8`。这是一个匹配，所以执行从`case 8`开始，直到最近的
`break`。

如果没有 **`break`** ，则继续执行下一个
**`case`** ，不做任何检查。

下面是一个没有`break`的例子:

```
let average = 2 + 6;

switch (average) {
  case 4:
    alert( 'Too small' );
  case 8:
    alert( 'Exactly!' );
  case 10:
    alert( 'Too big' );
  default:
    alert( "Incorrect values!" );
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们将看到三个`alerts` :
的顺序执行

```
alert( 'Exactly!' );
alert( 'Too big' );
alert( "Incorrect values!" ); 
```

Enter fullscreen mode Exit fullscreen mode

*   **getDay()方法切换案例**

getDay()方法以 0 到 6 之间的数字返回工作日。

(星期日=0，星期一=1，星期二=2，星期三=3，星期四=4，星期五=5，星期六=6)

此示例使用星期几来计算星期名称:

```
switch (new Date().getDay()) {
  case 0:
    day = "Sunday";
    break;
  case 1:
    day = "Monday";
    break;
  case 2:
     day = "Tuesday";
    break;
  case 3:
    day = "Wednesday";
    break;
  case 4:
    day = "Thursday";
    break;
  case 5:
    day = "Friday";
    break;
  case 6:
    day = "Saturday";
} 
```

Enter fullscreen mode Exit fullscreen mode

day 的结果将是以 day 格式表示的当前工作日

PS:这将根据你阅读这篇文章的时间而改变

我写这篇文章的时间是 2019 年 1 月 4 日，也就是星期五，所以结果应该是:

```
Friday 
```

Enter fullscreen mode Exit fullscreen mode

### 默认关键字

**default** 关键字指定没有大小写匹配时要运行的代码，
更像是一个 else 语句:

```
switch (new Date().getDay()) {
  case 6:
    text = "Today is Saturday";
    break; 
  case 0:
    text = "Today is Sunday";
    break; 
  default: 
    text = "Its not weekend yet!";
} 
```

Enter fullscreen mode Exit fullscreen mode

文本的结果将是:

```
Its not weekend yet! 
```

Enter fullscreen mode Exit fullscreen mode

**默认**情况不必是开关块中的最后一个情况:

```
switch (new Date().getDay()) {
  default: 
    text = "Its not weekend yet!";
    break;
  case 6:
    text = "Today is Saturday";
    break; 
  case 0:
    text = "Today is Sunday";
} 
```

Enter fullscreen mode Exit fullscreen mode

> **如果默认不是切换块中的最后一种情况，记得用一个分隔符结束
> 默认情况。**

### **结论**

开关案例的实际例子太多了，你可以点这里的
[来了解更多的开关案例。](https://javascript.info/switch)

如果这篇文章对你有帮助，分享出来吧！！

感谢阅读！

你可以在 twitter @iambolajiayo 上关注我