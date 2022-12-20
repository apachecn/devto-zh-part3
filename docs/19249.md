# JavaScript 中的条件语句

> 原文：<https://dev.to/kartik2406/conditional-statements-in-javascript-43hh>

[![Conditionals](img/e77ce68c9f1ec8c51c354ece923cc24c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BwvtV1W2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./https://thepracticaldev.s3.amazonaws.com/i/24t9ab0olr35gu98s8dx.jpg)

照片由延斯·勒利在 Unsplash 上拍摄

在这篇文章中，我们来看看什么是条件语句，不同类型的条件语句及其用例。

条件语句帮助您编写只在满足特定条件时才执行的代码。有两种类型的条件语句。

*   分支:选择一个可能的选项
*   循环:重复一个动作

### 分支

Javascript 有三个分支语句:

*   If else(-if)

当需要采用一个可能的路径时，可以使用 if else 表达式。

语法:

```
 if (expression) {
    //do something
  } else {
    // do something else
  } 
```

还可以有多个 else if 语句。

示例:

1.  检查变量是否为空

```
let val
if (val) {
  // do some processing
} else {
  // assign value, throw error
} 
```

1.  检查一个数是正数、负数还是零

```
if (val > 0) {
  console.log('Value is positve.')
} else if (val < 0) {
  console.log('Value is negative.')
} else {
  console.log('Value is zero.')
} 
```

*   Switch case 语句

switch 语句类似于 if else(if)，Switch 评估您的条件并匹配其中一个 case 语句。如果你的 else-if 链越来越长，你应该考虑切换到 switch case 语句。当使用 switch case 语句时，您可以访问另一个方便的关键字，即 *default* 关键字。当表达式与任何 case 语句都不匹配时，将执行 defualt 块中的代码。

语法:

```
switch (expression) {
  case 1: //do something
    break
  case 2:
    break

  default: // What to do when expression does not match any case
} 
```

每个 case 语句中都有一个 *break* 关键字，这样一旦找到匹配项，switch case 语句就会停止执行。

示例:

```
switch (color) {
  case 'red':
    console.log('Stop')
    break
  case 'green':
    console.log('Go')
    break
  case 'yellow':
    console.log('Slow down')
    break
  default:
    console.log('Invalid')
} 
```

*   条件运算符(？:)你会喜欢这个的语法，它清晰明了。

```
 expression ? code block 1: code block 2 
```

如果表达式的值为 true，将执行代码块 1，否则将执行代码块 2。

### 循环

Javascript 中有两种不同类型的循环:

1.  ####用于循环

当您预先知道迭代次数时，可以使用 for 循环。

语法:

```
 for(initilize expression, condition, increment expression){
    // awesome stuff
  } 
```

在 for 循环中有三个重要表达式

*   初始化:初始化循环计数变量
*   条件:在每次循环之前将被检查的表达式。
*   increment:每次循环后执行的表达式。

例子:

```
// display a message n times
for (let i = 0; i < 5; i++) {
  console.log('Loops are cool!')
}

let arr = [1, 2, 3, 4, 5]
for (let i = 0; i < 5; i++) {
  console.log(`Value at index ${i} is ${arr[i]}`)
} 
```

1.  While 循环

当您事先不知道迭代次数时，可以使用 while 循环。

语法:

```
while (expression) {
  //do something
} 
```

示例:
增加一个值，直到它超过一个限值

```
while (val < limit) {
  val += val
} 
```

while 循环有一个变体，即 do while 循环。不同之处在于语法和它将至少执行一次的事实，因为条件是在循环结束时检查的。

语法:

```
do {
  //some awesome stuff
} while (expression) 
```

### 中断并继续

没有这些关键字，循环将是不完整的。这些关键字允许我们在某种程度上控制循环。

break 关键字允许我们停止循环执行。你为什么想要这个？让我们来看看
从上面的
开始增加一个值代码

```
let val = 2
let limit = 50

while (val < limit) {
  val += val
  console.log(val)
}
/*
Output: 
4 
8 
16 
32 
64 // Opss
*/ 
```

明明 64 不应该出现在输出里，为什么还会出现？当瓦尔 32 岁的时候，循环表达式从 32 岁开始成立< 50 岁。现在要解决这个问题，我们需要在生成的新值上添加一个检查。

```
let val = 2
let limit = 50
while (val < limit) {
  let temp = val + val
  if (temp > limit) {
    break
  }
  val += val
}
/*
Output: 
4 
8 
16 
32 
*/ 
```

当 break 停止循环的所有后续步骤时，continue 语句只是跳过当前步骤。

让我们编写一段代码，打印数组中的非零元素。

```
let arr = [1, 0, 0, 2, 3, 0, 0, 4, 5, 0]
for (let i = 0; i < 10; i++) {
  if (arr[i] == 0) continue
  console.log(arr[i])
} 
```

这就是你所知道的分支和循环语句。做这些实验来掌握它们，这些是从长远来看对你有帮助的基础。

如果你喜欢这篇文章，请分享它。