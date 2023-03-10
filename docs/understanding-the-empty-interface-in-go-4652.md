# 理解 Go 中的空界面

> 原文：<https://dev.to/flrnd/understanding-the-empty-interface-in-go-4652>

首先，要了解更多关于接口的知识，建议阅读[如何在 go](https://jordanorelli.com/post/32665860244/how-to-use-interfaces-in-go) 和 [Go 数据结构中使用接口:接口](https://research.swtch.com/interfaces)。

# 现在，*的空界面*是怎么回事？

假设我们正在开发某种输出打印库。这样，我们开始思考如何设计我们的打印功能。该函数需要接收一些值，然后将结果传递给标准输出。让我们看一个例子( [go 游乐场链接](https://play.golang.org/p/JRFvPPzWaY4) ):

```
// This function takes a string as a parameter and print it to the standard output
func Println(format string) (int n, err error) {
    return fmt.Fprintln(os.Stdout, format)
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们想打印一个数字会发生什么呢？还是更复杂的输入？运行 [go 游乐场](https://play.golang.org/p/36UPWg3ps04) :
中的示例

```
package main

import (
    "fmt"
    "os"
)

func Println(format string) (n int, err error) {
    return fmt.Fprintln(os.Stdout, format)
}

func main() {
    Println("Number: ", 10)
}

--------
// And this is what happen if we run our code, as we could expect:
prog.go:14:9: too many arguments in call to Println
    have (string, number)
    want (string)

Go build failed. 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所料，我们的计划失败了。我们传递两个参数，并且我们定义我们的函数只接收一个类型为`string`的参数。

那么，我们如何求解我们的 Println 函数呢？我们是否应该为想到的每个疯狂的想法实现每个用例？谢天谢地，我们没有。

Go 已经处理了像这样的情况，这时空接口就派上用场了。实际上，`fmt`包函数就是这样实现的。

首先，让我们检查一下`empty interface`的定义(来自[一场围棋赛](https://tour.golang.org/methods/14)):

> 指定零个方法的接口类型称为空接口:
> 
> 空接口可以保存任何类型的值。(每个类型至少实现零个方法。)
> 
> 处理未知类型值的代码使用空接口。比如 fmt。Print 接受任意数量的 interface{}类型的参数。

好了，让我们看看 [fmt 包文档](https://golang.org/pkg/fmt/#Fprintln)，这是我们之前在 Println:
`func Fprintln(w io.Writer, a ...interface{}) (n int, err error)`中使用的 Fprintln 函数的规范

第二个参数中的`a ...interface{}`意味着我们接收从 1 到 N 的`empty interface`类型值(对于熟悉 Javascript spread 操作符的人来说)。如果我们再次引用这个定义:

`Empty interfaces are used by code that handles values of _unknown type_.`

好的，*未知类型*，对于我们的打印函数，我们真的不关心它是什么类型，对吗？我们只担心把它写到标准输出中。

所以，就是这样，这样我们就不需要担心实现每一个用例，因为 Go 在运行时为我们处理那些`unknown types`的转换，现在我们可以这样做:`Println("Hello ", 10, &v)`而不用担心参数。

注意:我使用 fmt 实现进行解释，因为空接口是我开始学习围棋时遇到的最令人困惑的事情之一，在 go playground 中使用它帮助我理解了如何以及为什么使用它。

注 2:这将是我们的最终计划:

```
package main

import (
    "fmt"
    "os"
)

// This function is a exact implementation of fmt.Println()
func Println(a ...interface{}) (n int, err error) {
    return fmt.Fprintln(os.Stdout, a... )
}

func main() {
       n := 10                 // number
       s := "Hello!"           // string
       r := []rune("もしもし！")// slice of runes

    // Our final Println accepts different types of values
    // and go translate them in run time.
    Println(s, n, r)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有睡着就走了这么远，感谢你的阅读！🤓 🖖

封面照片: [*菠萝供应公司@unsplash*](https://unsplash.com/@pineapple)