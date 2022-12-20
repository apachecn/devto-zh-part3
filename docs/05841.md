# 围棋中延迟的力量:入门

> 原文：<https://dev.to/asanchez/the-power-of-defer-in-go-on-entry-43ol>

这里有一个小技巧，我希望我在开始用 Golang 编程时就已经学会了:在任何函数的开始和退出中都使用 defer。

虽然这是一个非常基本的概念，在调试代码时会非常有帮助，但在互联网上可以找到的大多数 Golang 教程中，很难找到关于它的任何信息。

[延迟](https://golang.org/ref/spec#Defer_statements)是你开始用 Go 编程时要学的第一件事。这非常简单:在任何函数中，您都可以给其他函数加上前缀`defer`,以确保这个函数在父函数退出之前被执行，无论如何。即使父函数死机，这个延迟的函数也会运行。

如果你对`defer`不熟悉，在继续阅读本文之前先看看这里:[举例:推迟](https://gobyexample.com/defer)。

然而，有些东西你可能没有在你找到的任何例子或教程上读到过:你也可以在任何函数的开始和结束处使用 defer 来对代码进行配对。

要使用这一点，创建一个函数，并使其返回另一个函数本身，这将是真正的延迟函数。延迟这个父函数，在延迟的时候给它加额外的括号，像这样:

```
func main() {
    defer greet()() 
    fmt.Println("Some code here...")
}

func greet() func() {
    fmt.Println("Hello!")
    return func() { fmt.Println("Bye!") } // this will be deferred
} 
```

Enter fullscreen mode Exit fullscreen mode

哪个输出这个:

```
Hello!
Some code here...
Bye! 
```

Enter fullscreen mode Exit fullscreen mode

延迟函数返回的函数将是真正的延迟函数。延迟函数中的其他代码将在函数开始时执行(或者放在任何地方)。

这有什么了不起的？正如您在下一个例子中看到的，您可以在延迟函数中使用变量，这些变量在第一次执行和延迟执行时都可以被访问:

```
func main() {
    example()
    otherExample()
}

func example(){
    defer measure("example")()
    fmt.Println("Some code here")
}

func otherExample(){
    defer measure("otherExample")()
    fmt.Println("Some other code here")
}

func measure(name string) func() {
    start := time.Now()
    fmt.Printf("Starting function %s\n", name)
    return func(){ fmt.Printf("Exiting function %s after %s\n", name, time.Since(start)) }
} 
```

Enter fullscreen mode Exit fullscreen mode

哪个输出这个:

```
Starting example
Some code here
Exiting example after 0s
Starting otherExample
Some other code here
Exiting otherExample after 0s 
```

Enter fullscreen mode Exit fullscreen mode

可以想象，推迟代码在任何函数的入口和出口使用它会非常有用，特别是对于调试代码。

这里可以看到另一个例子:[https://github . com/adonovan/gopl . io/blob/master/ch5/trace/main . go](https://github.com/adonovan/gopl.io/blob/master/ch5/trace/main.go)