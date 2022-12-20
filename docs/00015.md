# 围棋中的条件句

> 原文：<https://dev.to/codetips/conditionals-in-go-d49>

[![Conditionals in Go](img/ee6709db999f396ce1f7483c463ba33e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ASx_jNMF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1533073526757-2c8ca1df9f1c%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在上一篇文章中(“[数组和 Go](https://www.codetips.co.uk/languages/go/arrays-and-loops-in-go/) 中的循环”)，我们创建了一个程序，输出我们的`name`、`age`、`profession`、`favouriteAnimal`和一个`hobbies`列表。

在本文中，我们将扩展我们的程序，根据`age`变量做出不同的行为。在我们开始之前，让我们删除一些在本文中不需要的代码:

```
package main

import "log"

func main() {

    // Start of our code

    name := "Simon"
    // age := 29

    hobbies := [4]string{"example1", "mountain biking", "example2", "example3"}

    log.Printf("Hello World. My name is %s.", name)

    log.Printf("I have %d hobbies, and they are: ", len(hobbies))

    for i := 0; i < len(hobbies); i++ {
        log.Println(hobbies[i])
    }

    // End of our code

} 
```

**注意:**我们已经注释掉了`age`参数。Go 编译器的规则之一是你不能声明任何未使用的变量。如果我们声明了它(`age := 29`)但没有引用它——它被认为是未使用的，当运行程序时你会看到一个错误。

现在运行应用程序，您应该会看到以下输出:

```
$ go run first.go 
2019/06/04 07:19:37 Hello World. My name is Simon.
2019/06/04 07:19:37 I have 4 hobbies, and they are: 
2019/06/04 07:19:37 example1
2019/06/04 07:19:37 mountain biking
2019/06/04 07:19:37 example2
2019/06/04 07:19:37 example3 
```

我们现在将带回`age`变量，根据它的值，我们将打印以下内容之一:

*   如果`age`小于 10，我们将打印“我年轻 x 岁”。
*   如果`age`大于 10，我们将打印“我 x 岁”。

为此，我们将使用一个`if`语句。在 Go 中声明`if`语句的语法很简单:

```
if expression {
    // Run this code if expression evaluates to true
} 
```

为了执行依赖于`age`的动作，我们可以简单地在我们的代码中添加两个`if`语句(**注意:**我们已经省略了所有与`age`无关的代码，在这个阶段，把重点放在我们正在讨论的内容上)

```
package main

import "log"

func main() {

    // Start of our code

    age := 29

    if age < 10 {
        log.Printf("I am %d years young", age)
    }

    if age > 10 {
        log.Printf("I am %d years old", age)
    }

    // End of our code

} 
```

我们现在执行两项检查:

*   年龄小于(`<` ) 10 吗？如果是，我们打印出第一条消息。
*   年龄是否大于(`>` ) 10？如果是，我们打印出第二条消息。

完美！事实上，不幸的是没有。这段代码有两个错误。

**问题 1**

我们正在检查`age`是否小于(`<`)并且大于(`>` ) 10，但是如果`age`是 10 呢？它既不叫`Print`语句。

```
$ go run conditionals.go 
```

为了解决这个问题，我们首先需要决定如果这个人的年龄是 10 岁，我们应该打印什么声明。我认为 10 仍然很小，所以让我们打印出第一个语句。如果你不同意，看看能不能把第二条语句打印出来。

然后，我们需要将“小于”(`<`)条件改为“小于或等于”(`<=`)。

```
package main

import "log"

func main() {

    // Start of our code

    age := 10

    if age <= 10 {
        log.Printf("I am %d years young", age)
    }

    if age > 10 {
        log.Printf("I am %d years old", age)
    }

    // End of our code

} 
```

现在，如果我们用 10 的`age`来运行我们的程序，我们就可以打印出我们的消息了！

```
$ go run conditionals.go
2019/06/04 17:36:14 I am 10 years young 
```

**问题二**

不管赋予`age`什么值，我们**总是**检查两件事:

*   值是否小于或等于 10？
*   值是否大于 10？

在这种情况下`age`只能匹配这些表达式中的一个，所以我们要求计算机做额外的工作。这看起来没什么大不了的，但是如果我们的应用程序增加了成千上万的不必要的检查，我们会看到一个很大的性能损失。

我们可以使用一个`if/else`语句来代替两个`if`语句。声明一个`if/else`语句的语法非常简单:

```
if expression {
    // Run this code if expression evaluates to true
} else {
    // Run this code if expression evaluates to false
} 
```

正如我们之前所说的,`age`变量只会小于或等于 10，或者大于 10。因此，我们只需要检查其中一个条件，其他条件都符合`else`语句:

```
package main

import "log"

func main() {

    // Start of our code

    age := 10

    if age <= 10 {
        log.Printf("I am %d years young", age)
    } else {
        log.Printf("I am %d years old", age)
    }

    // End of our code

} 
```

花些时间来玩一玩代码——当你准备好继续的时候，我们还会在这里。

准备好了吗？

非常好。让我们更进一步，让我们的节目更有“年龄意识”。这次我们将检查四种可能的结果:

*   如果`age`变量小于 13，我们将打印“我被认为是一个孩子”
*   如果`age`变量在 13 和 19 之间，我们将打印“我被认为是青少年”
*   如果`age`变量在 19 到 69 之间，我们将打印‘我被认为是成年人’。
*   如果`age`变量是 70 或更大，我们将打印“我被认为是一个养老金领取者”。

我们已经知道拥有四个独立的`if`语句不是一个好主意，但是一个`if/else`语句只能处理两种结果，那么我们现在该怎么办呢？我们可以用`if/else if/else`的说法！

声明`if/else if/else`语句的语法非常简单:

```
if expression {
    // Run this code if expression evaluates to true
} else if anotherExpression {
    // Run this code if anotherExpression evaluates to true
} else if yetAnotherExpression {
    // Run this code if yetAnotherExpression evaluates to true
} else {
    // Run this code if none of the above expressions have evaluated to true
} 
```

`else`和`else if`的区别在于后者允许我们指定另一个表达式。

如果第一个表达式还没有计算到`true`，那么检查下一个表达式。这一直持续到一个表达式的计算结果为`true`、一个`else`语句或条件语句结束。

如果表达式的计算结果为真，它将跳过其余的`else if`和`else`语句。

我们可以很容易地在我们的程序中使用这个逻辑，而不需要学习许多新概念:

```
package main

import "log"

func main() {

    // Start of our code

    age := 70

    if age < 13 {
        log.Println("I am considered a child")
    } else if age >= 13 && age < 20 {
        log.Println("I am considered a teenager")
    } else if age >= 20 && age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    // End of our code

} 
```

让我们一个接一个地看一下这些表达:

*   `if age < 13` -我们检查`age`是否小于(`<` ) 13。如果是，我们打印出“子”语句，并且不必执行任何更多的检查。
*   `else if age >= 13 && age < 20` -我们检查`age`是否大于等于(`>=` ) 13 **，** ( `&&` ) `age`小于 20。这给了我们一个 13 - 19 的范围。如果表达式的值为`true`，我们打印出“青少年”语句，并且不需要执行任何更多的检查。
*   `else if age >= 20 && age < 70` -我们检查`age`是否大于等于(`>=` ) 20 **，以及** ( `&&`)年龄是否小于 70。这给了我们一个 20 - 69 的范围。如果表达式的值为`true`,我们打印出“成人”语句，并且不必执行任何更多的检查。
*   `else` -如果`age`是任何其他值(即 70 或以上)，我们打印出“养老金领取者”声明。

现在让我们将本文开头的代码添加回来:

```
package main

import "log"

func main() {

    // Start of our code

    name := "Simon"
    age := 29
    hobbies := [4]string{"example1", "mountain biking", "example2", "example3"}

    log.Printf("Hello World. My name is %s.", name)

    if age < 13 {
        log.Println("I am considered a child")
    } else if age >= 13 && age < 20 {
        log.Println("I am considered a teenager")
    } else if age >= 20 && age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    log.Printf("I have %d hobbies, and they are: ", len(hobbies))

    for i := 0; i < len(hobbies); i++ {
        log.Println(hobbies[i])
    }

    // End of our code

} 
```

就这样——我们现在已经在我们的 Go 程序中添加了一个`if/else if/else`语句！在以后的文章中，我们将看看如何用一个叫做`switch`语句的不同条件语句来实现这一点。

出发前，看看你能否完成以下挑战:

*   可以进一步优化最后一条`if/else if/else`语句，看看您是否能找出方法。**提示:**我们仍然要求计算机做更多不必要的工作。

如果你需要另一个提示，或者想向我们展示你的解决方案，请联系社交媒体( [Twitter](https://twitter.com/RealCodeTips) 、[脸书](https://www.facebook.com/CodeTips-2884823445075145/)、 [LinkedIn](https://www.linkedin.com/company/codetips) 、 [Instagram](https://www.instagram.com/realcodetips/) )或者加入我们的 [Slack group](https://join.slack.com/t/realcodetips/shared_invite/enQtNjQyNTg3MTM3MzAxLTc3ZmUzMDRmMzViNTUxZDdjOGVmMDM0YzVlYzI3ZGQ4NzM3ZWE2ZDk2NGU4ODBkMjIzZjdjOTUxZmU4Nzc3MjQ) 。