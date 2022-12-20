# 短路分配

> 原文：<https://dev.to/dr_sam_walpole/short-circuit-assignment-25ik>

# 在 Javascript 中给变量赋予默认值

当构建网站或 web 应用程序时，屏幕上显示的数据通常是根据一些用户定义的输入动态创建的。有时这种输入会丢失或不符合预期。在这些情况下，最好使用默认值，这样我们的应用程序就不会中断或出现意外行为。

下面是一个使用逻辑运算符*或*给变量赋值的例子，称为短路赋值:

```
function greet(name) {
  const greeting = name || "Person";
  return "Hello " + greeting;
}

greet("Sam"); // returns "Hello Sam" 

greet(); // returns "Hello Person" 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？为了理解这一点，我们先来快速浏览一下逻辑运算符。

## 逻辑运算符

逻辑运算符对表达式求值并返回布尔值(`true`或`false`)。在 Javascript 中有 3 个逻辑运算符:

### *和*运算符(& &)

如果两个输入都是`true`，则评估为`true`。否则返回`false`。

```
true && true; // returns true
true && false; // returns false
false && false; // returns false 
```

Enter fullscreen mode Exit fullscreen mode

### *或*运算符(||)

如果一个或两个输入为`true`，则评估为`true`。否则返回`false`。

```
true || true; // returns true
true || false; // returns true
false || false; // returns false 
```

Enter fullscreen mode Exit fullscreen mode

### *不是*符(！)

返回与输入相反的布尔值。

```
!true // returns false
!false // returns true 
```

Enter fullscreen mode Exit fullscreen mode

### if 语句中的逻辑运算符

逻辑运算符的常见用例是根据这些运算符的计算结果有条件地执行代码。

```
if (isA() && isB()) { // Do something } 
// if both isA() and isB() return true, then execute code

if (isA() || isB()) { // Do something } 
// if either or both isA() and isB() return true, then execute code

if (!isA()) { // Do something } 
// if isA() doesn't return true, then execute code 
```

Enter fullscreen mode Exit fullscreen mode

然而，当我们在一个`if`语句中放入非布尔值时会发生什么呢？

```
if ("hello world") { // Do something } 
// code is executed 

if ("") { // Do something } 
// code is not executed 

if (5) { // Do something } 
// code is executed 

if (0) { //Do something } 
// code is not executed 
```

Enter fullscreen mode Exit fullscreen mode

这意味着这些表达式中有些计算结果为`true`，有些计算结果为`false`。事实上，所有表达式都可以被评估为`true`(称为真值)或`false`(称为假值)。

## 真假值

Falsey 值是那些在`if`语句中评估为`false`的值。在 Javascript 中，只有 6 个 falsey 值:

*   错误的
*   Zero
*   " "(空字符串)
*   空
*   不明确的
*   圆盘烤饼

所有其他的值，无论是字符串、数字还是对象(当然还有`true`布尔值)，计算结果都是`true`，被称为真值。

## 逻辑运算符和非布尔值

那么，当我们将非布尔值(可以是真或假)放入逻辑表达式时，会发生什么呢？

```
"hello" && "world"
// returns "world" 

"hello" || "world"
// returns "hello" 
```

Enter fullscreen mode Exit fullscreen mode

在第一个例子中，我们看到`"hello" && "world"`的结果其实是`"world"`。这是因为*和*操作符需要测试表达式两边的真实性——两个值都必须是`true`,整个表达式才是`true`。然后，它返回它计算的最后一个值，在本例中，如果表达式计算结果为`true`，则返回`"world"`。

这对*或*来说不太一样。由于*或*只需要其中一个值为真，以使整个表达式为真，如果第一个值为真，那么它可以提前退出评估，只测试第一个值，然后返回第一个值。这被称为**短路评估** -如果第一个值是`true`，没有必要检查表达式的其余部分，所以它只测试第一个值就短路。

然而，如果第一个值的计算结果是`false`，那么*或*语句必须检查第二个值，看它是否是`true`。

```
0 || 1
// returns 1 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`0`的计算结果为`false`，因此*或*语句必须检查第二个值的真值- `1`是真值，因此从表达式中返回`1`。

通过这种方式，我们可以指定默认值。我们可以使用用户定义的变量作为第一个值，使用默认值作为第二个值。如果用户给出一个真实的输入，他们的输入被返回，因为*或*语句短路。在用户没有输入的情况下，第一个变量将是 falsy，因此返回第二个值。

```
const userVariable = "user value";
const value = userVariable || "default value"
// value is equal to "user value"

const userVariable = "";
const value = userVariable || "default value"
// value is equal to "default value" 
```

Enter fullscreen mode Exit fullscreen mode

我希望这篇文章对你有用。请通过推特让我知道你的评论/问题。

*   [@dr_sam_walpole](https://dev.to/dr_sam_walpole) 如果你愿意，也可以[给我买杯咖啡](https://ko-fi.com/samwalpole)！😊