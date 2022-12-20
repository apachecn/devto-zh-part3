# 套餐和“主”的重要性

> 原文：<https://dev.to/codetips/packages-and-the-importance-of-main-2ik7>

[![Packages and the importance of “main”](img/6ccada03f7d9dead5b75890749017d60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8J3xaUbM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1474625121024-7595bfbc57ac%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在[上一篇文章](https://www.codetips.co.uk/languages/go/writing-your-first-go-programme/)中，你创建了你的第一个围棋程序，我们以下面的代码结束:

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

我们着重于声明变量，在句子中使用它们，并输出到终端，但是有两个重要的概念我们没有解释。

## 套餐

Go 由内部和 3 个 <sup>rd</sup> party 包组成。内部软件包是 Go 安装的一部分，而第 3 <sup>第 3</sup>团队软件包是由 Go 社区开发的，必须单独安装。安装 3 个 <sup>rd</sup> 派对包不在本文讨论范围内。

软件包允许开发者组织和重用代码，并且可以通过“导入”它们在你的程序中使用。在上面的例子中我们导入了一个名为`log`的内部包，它包含了许多可以用来输出到终端的函数；我们使用了其中的两个函数——`Println`和`Printf`。

```
import “log” 
```

开发者可以开发自己的包，并在文件的第一行使用`package`声明来命名。我们将在以后的文章中探讨这个问题。

# “主”的重要性

你可能已经注意到我们上面的例子在两个地方有单词`main`;包名和函数名。

当你运行一个 Go 程序(`go run main.go`)时，它会通过首先找到主包，然后找到主函数并运行其中的代码来寻找一个入口点(应该从这个入口点开始运行代码)。

如果您更改了包名或函数名，编译器会抛出一个错误。

我们也把我们的文件命名为`main.go`，但是实际上你可以给它起任何名字。

我们现在已经建立了一个新的 Slack 社区，以更实时地帮助人们。你可以在这里加入我们。