# 什么是 switch 语句？

> 原文：<https://dev.to/codetips/what-is-a-switch-statement-5cne>

> switch 语句评估一个条件，并根据值提供多个决策点。

[![What is a switch statement?](img/c95460ceb1bb0ab55c29bd13ac4bac7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fBd-RYnT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1429743305873-d4065c15f93e%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在之前的文章中，我们已经讨论了 [if](https://codetips.co.uk/beginner/what-is-an-if-statement/) 语句和 [if/else](https://codetips.co.uk/beginner/what-is-an-else-statement/) 语句。我们讨论的例子，展示了多个决策点，是检查天气和决定穿什么鞋。

```
if (raining) {
    // put on wellies
} else-if (snowing) {
    // put on snow boots
} else-if (sunny) {
    // put on sandals
} else {
    // stay indoors
} 
```

我们只评估了三种结果，`raining`、`snowing`和`sunny`，用`else`语句来捕捉所有其他类型的天气，这已经很难理解了。

如果我们想创建一个具有更多可能性的检查，例如检查一周中的某一天并执行适当的杂务，该怎么办？

```
// Get the current day of the week
var day = getCurrentDay()

if (day == "Monday") {
    // Perform Monday chores
} else-if (day == "Tuesday") {
    // Perform Tuesday chores
} else-if (day == "Wednesday") {
    // Perform Wednesday chores
} else-if (day == "Thursday") {
    // Perform Thursday chores
} else-if (day == "Friday") {
    // Perform Friday chores
} else-if (day == "Saturday") {
    // Perform Saturday chores
} else-if (day == "Sunday") {
    // Perform Sunday chores
} else {
    // Unknown day
} 
```

我们仍然没有评估那么多的结果，但它变得越来越难以阅读。想象一下，如果我们更进一步，评估周数，需要评估 52 个结果。

进入`switch`语句。它非常类似于`if`语句，因为它接受一个条件并提供多个决策点，但是它更容易阅读和维护。

让我们以上面的星期几为例，将其转换为 switch 语句。

```
// Get the current day of the week
var day = getCurrentDay()

switch (day) {
  case "Monday":
    // Perform Monday chores
  case "Tuesday":
    // Perform Tuesday chores
  case "Wednesday":
    // Perform Wednesday chores
  case "Thursday":
    // Perform Thursday chores
  case "Friday":
    // Perform Friday chores
  case "Saturday":
    // Perform Saturday chores
  case "Sunday":
    // Perform Sunday chores
  default:
    // Unknown Day
} 
```

如何编写`switch`语句的更详细的细节将在以后特定语言的文章中讨论，但是概念与`if`语句相同；它检查一个条件(在这个例子中是`day`的值)并对照每个`case`语句进行检查。如果条件匹配`case`语句，它将评估该部分的代码。

就像一个`else`语句一样，`switch`中的`default`语句为不匹配任何`case`语句的任何内容提供了一个总括。