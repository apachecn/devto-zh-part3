# 编写你的第一个围棋程序

> 原文：<https://dev.to/codetips/writing-your-first-go-programme-35nm>

> 到本文结束时，您将已经编写了一个 Go 程序，它会根据一些变量输出一个句子。

[![Writing your first Go programme](img/63b28e5a9515521ed9240e7eb936b2ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wXNQ9Z5N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1526374965328-7f61d4dc18c5%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

如果你已经阅读了这篇文章，你应该已经阅读了 [Go 简介](https://www.codetips.co.uk/languages/go/go-introduction/)，并且熟悉了[变量](https://www.codetips.co.uk/beginner/what-is-a-variable/)和[数据类型](https://www.codetips.co.uk/beginner/what-is-a-data-type/)。

在 Go 中声明变量的[语法](https://www.codetips.co.uk/beginner/what-is-syntax/)定义了几种声明变量的方式，我们将把它们分成两组。

### 明确键入

如果程序员在声明一个变量时指定了一个[类型](https://codetips.co.uk/beginner/what-is-a-data-type/)，就说这个变量是显式类型化的。

在以下示例中:

*   `x`被声明为`string`,`x`的值为`"Hello World"`。
*   `y`被声明为`bool`，但没有赋值*。
*   `a`和`b`都被声明为`int`，并被赋予各自的赋值(`a=1`、`b=2`)。

```
var x string = "Hello World"

var y bool

var a, b int = 1, 2 
```

### 推断类型

如果程序员在声明一个变量的时候没有指定一个[类型](https://codetips.co.uk/beginner/what-is-a-data-type/)，那么就说这个变量的类型是从赋值中推断出来的。

在以下示例中:

*   `x`被赋予值`"Hello World"`，并具有推断类型`string`。
*   `y`被赋予值`true`，并具有推断类型`bool`。

```
var x = "Hello World"

y := true 
```

如果你需要绝对确定一个变量是一个特定的[数据类型](https://www.codetips.co.uk/beginner/what-is-a-data-type/)(例如，一个`float`而不是一个`int`)，使用显式类型的方法，否则，使用常识在每种情况下哪一个更好。

*当变量声明时没有初始值，它们被赋予零值。这是针对数值类型的`0`，针对布尔类型的`false`，针对字符串的`""`(一个空字符串)。

* * *

### 你的第一个围棋程序

终于到了编写你的第一个围棋程序的时候了。当[安装](https://golang.org/doc/install) Go 的时候，你已经设置好了你的 Go 目录(比如 Linux 环境中的`/usr/local/go`)。

在这个目录中，在`/src/firstApplication`下创建一个新目录(例如，完整的 Linux 路径将是`/usr/local/go/src/firstApplication`)，并在您选择的代码编辑器中打开它。

我们将省略 Go 的大部分细节，这样你就不会被太多的信息淹没，但是一切都将在以后的文章中解释。

在新的`firstApplication`目录中，创建一个名为`main.go`的新文件，将下面的代码添加到其中，并保存该文件。

```
package main

import "log"

func main() {

    // Start of our code

    log.Println("Welcome to my first Go programme.")

    // End of our code

} 
```

现在，打开一个终端，导航到您的`firstApplication`文件夹，并运行以下命令:

```
go run main.go 
```

您的输出应该与下图非常相似。

[![Writing your first Go programme](img/46b2a96f38138dafd78858e2d59d623d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9s__8VrQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/05/image-6.png)

如您所见，`log.Println`函数向终端输出文本。不幸的是，作为第一个节目，这是非常无聊的。让我们用[变量](https://www.codetips.co.uk/beginner/what-is-a-variable/)让这个更个人化！

用以下代码替换`main.go`的内容:

```
package main

import "log"

func main() {

    // Start of our code

    var name string = "Simon"
    var age int = 29
    var profession string = "Developer"

    log.Println("Welcome to my first Go programme.")
    log.Printf("My name is %s", name)
    log.Printf("I am %d years old", age)
    log.Printf("I work as a %s", profession)

    // End of our code

} 
```

这次我们输出四个不同的句子。前一个例子中的一个和另外三个使用了一个叫做`Printf`的新函数。这个函数允许程序员传入一个或多个变量；通过指定变量[、数据类型](https://www.codetips.co.uk/beginner/what-is-a-data-type/)(对于`string`为`%s`，对于`integer`为`%d`)。

现在看看你能否完成以下挑战:

1.  改变变量，让程序打印出你的姓名、年龄和职业。
2.  改变程序，用一句话输出所有内容，而不是四个不同的句子。
3.  添加一个新变量`favouriteAnimal`，并将其合并到您输出的句子中。

请发微博给我们，地址是 [@RealCodeTips](https://twitter.com/RealCodeTips) ，并向我们展示您的解决方案。如果你中途遇到困难，请伸出援手，我们会尽力帮助你。