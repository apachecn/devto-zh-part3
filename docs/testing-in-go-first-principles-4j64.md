# 围棋测试:首要原则

> 原文：<https://dev.to/fteem/testing-in-go-first-principles-4j64>

如果你有任何编程经验，无论是作为学生还是专业人士，你很可能听说过测试。这是一个无处不在的话题，无论是在会议、书籍还是文章中。(看到我在那里做了什么吗？)

此外，这似乎是每个人都同意的话题——是的，测试是好的，我们应该这样做。人们认为测试对你的代码有好处有很多原因。但是，在我们深入兔子洞讨论测试的利与弊之前，让我们学习一下如何测试我们的 go 代码。

当然，通过实际的例子，你可以跟随。

## 什么是测试？

现在，在我们继续之前，我将带你进行一次短暂的回忆之旅。在我职业生涯的这个阶段，我可能已经编写了数千个(如果不是数万个)测试用例。我在测试中失败过很多次，尤其是作为一个新手。因此，如果您是测试新手，或者还没有掌握它——不要担心，我会为您做好准备。

在你开始测试之前，我想让你知道一个想法:就像你的程序是由代码组成的一样，你的测试也是代码。真的，这是你必须记住的。很简单——测试是代码。

这个测试代码所做的是调用为您的程序提供动力的代码，并检查它返回的是否是预期的。很简单，不是吗？

这一切都围绕着设定期望，然后让你的程序满足这些期望。通常语言为你提供测试代码的包或库，每种语言或库在测试方面都有自己的约定。但真的，只是-满足期望。

简而言之，这就是测试。我们继续吧。

## 什么是测试？

那么，什么是测试呢？

这很简单——通过可重复的步骤，可以验证某个东西是否如预期的那样工作。

围棋术语中的测试是什么？

同样，这是一段你可以多次运行的代码，它会检查你的代码是否按预期运行。

就是这样。我们要写一个吗？

## 测试我们的 Go 代码

要编写一个测试，我们首先要有一个要测试的程序。让我们实现一个简单的函数，它将获取`int`的一部分并返回其最大的数字:

```
func Max(numbers []int) int {
    var max int

    for _, number := range numbers {
        if number > max {
            max = number
        }
    }

    return max
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单。我们取一部分`int` s 并返回最大的。那么，我们如何测试我们的代码如预期的那样工作呢？

让我们编写一个函数，它有两个参数:一段`int`和该段中`int`的最大值。我们就叫它`TestMax` :

```
func TestMax(numbers []int, expected int) string {
    output := "Pass"
    actual := Max(numbers)
    if actual != expected {
        output = fmt.Sprintf("Expected %v, but instead got %v!", expected, actual)
    }
    return output
} 
```

Enter fullscreen mode Exit fullscreen mode

`TestMax`函数将检查`Max`函数调用的结果是否与`expected`结果匹配。如果是，它将简单地返回`"Pass"`，否则它将返回一个信息性的字符串，其中包含它所期望的和它所得到的。

让我们在我们的`main`函数中使用它:

```
func main() {
    fmt.Println(TestMax([]int{1, 2, 3, 4}, 4))
} 
```

Enter fullscreen mode Exit fullscreen mode

这里,`main`函数将调用一次`TestMax`,其中一个切片包含作为参数的`1,2,3,4`和作为预期最大值的`4`。

你可能已经在想这个例子会过去的。让我们运行它:

```
$ go run max.go
Pass 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，这个例子通过了。再来加两个:

```
func main() {
    fmt.Println(TestMax([]int{1, 2, 3, 4}, 4))
    fmt.Println(TestMax([]int{4, 2, 1, 4}, 3))
    fmt.Println(TestMax([]int{0, 0, 0, 0}, 1))
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们再添加两个例子，有两对不同的参数:

1.  包含预期最大值`4,2,1,4`和`3`的切片
2.  包含四个零的片，以及作为预期最大值的`1`

如果我们运行它，这两个例子都会失败。失败的原因是我们添加的两个`int`片段都不匹配我们作为第二个参数传递给两个调用的预期最大值。在第二个例子中，最大值是`4`，而我们期望的是`3`。在第三个例子中，最大值是`0`，而我们期望的是`1`。

测试我们的`Max`函数可以只用一个函数(`TestMax`)来完成。只要我们能够/为函数提供一个输入和一个预期的输出，我们就可以测试我们的函数。

这里需要理解的重要一点是，测试可以非常简单。在这里，我们能够检查我们的功能是否如预期的那样工作，而不需要任何花哨的框架和库——只需要普通的 Go 代码。如果你有任何测试经验，你已经知道这种方法不能扩展太远，但是理解测试只是代码的想法是非常好的。

使用这种方法，我们甚至可以在技术上编写我们自己的测试框架/库。好在 Go 的标准库中已经包含了一个测试包，所以我们可以避免这个问题。

## 用 Go 的`testing`包测试

Golang 的 [`testing`](https://golang.org/pkg/testing/) 包提供了对 Go 包自动化测试的支持。它公开了一组有用的函数，我们可以用它们来获得一些好处:更好看的代码、标准化的测试方法和更好看的输出。此外，我们不再需要创建我们自己的失败/通过测试的报告。

让我们看看如何使用`testing`包测试我们的`Max`函数。

首先，我们需要创建一个`max_test.go`文件，它将是我们的`max.go`的测试副本(这里定义了我们的`Max`函数)。最重要的是，这两个文件必须是同一个包的一部分(在我们的例子中是`main` ):

```
// max_test.go
package main

import "testing"

func TestMax(t *testing.T) {
    actual := Max([]int{1, 2, 3, 4})
    if actual != 4 {
        t.Errorf("Expected %d, got %d", 4, actual)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

导入的`testing`包允许基准测试和测试。在我们的测试中，我们使用了`testing.T`类型，当传递给测试函数时，它管理测试状态并格式化测试日志。

在`TestMax`函数中，我们得到`Max`函数的结果，并将其分配给`actual`。然后，我们将`actual`与预期结果(`4`)进行比较。如果比较失败，那么我们通过使用`t.Errorf`函数使测试失败，并提供错误消息。

## 当我们运行时会发生什么`go test`

Golang 的`testing`包还附带了它的伙伴——`go test`命令。该命令自动测试由导入路径命名的包。`go
test`重新编译每个包以及任何文件名与文件模式`*_test.go`匹配的文件。

要运行这个文件，我们必须使用`go test`命令:

```
› go test
PASS
ok github.com/fteem/testing_in_go   0.007s 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们不需要告诉 Golang 运行哪些测试——它自己就能解决这个问题。这是因为`go test`做得很巧妙，有两种不同的运行模式。

第一种模式称为本地目录模式。当调用不带参数的命令时，此模式处于活动状态。在本地目录模式下，`go test`将编译包源代码和在当前目录中找到的测试，然后运行结果测试二进制文件。

包测试完成后，`go test`打印一个摘要行，显示测试状态(`ok`或`FAIL`)、包名和运行时间。这就是我们在输出中实际看到的——我们的测试已经通过。查看上面的输出，我们还可以看到它们传入了`0.007s`。相当快！

第二种模式称为包列表模式。当使用显式参数调用命令时，此模式被激活。在这种模式下，`go test`将编译和测试作为参数列出的每个包。如果包测试通过，`go test`只打印最后的“ok”摘要行。如果包测试失败，go test 将打印完整的测试输出。

现在，我们可以坚持使用第一种模式。我们将在下一篇文章中看到何时以及如何使用第二种模式。

## 处理测试失败

现在我们有了一个通过的例子，让我们看看测试失败是什么样子的。让我们添加另一个例子，我们会故意失败:

```
func TestMaxInvalid(t *testing.T) {
    actual := Max([]int{1, 2, 3, 4})
    if actual != 5 {
        t.Errorf("Expected %d, got %d", 5, actual)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`TestMaxInvalid`与我们之前的测试函数非常相似，唯一的区别是有错误的预期。简单地说——我们知道`Max`将返回`4`，但是我们期待的是`5`。

现在，让我们再添加一个例子，我们将一个空的片作为参数传递给`Max`，并期待`-1`的结果:

```
func TestMaxEmpty(t *testing.T) {
    actual := Max([]int{})
    if actual != -1 {
        t.Errorf("Expected %v, got %d", -1, actual)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们再次运行`go test`，看看输出:

```
$ go test
-------- FAIL: TestMaxInvalid (0.00s)
    max_test.go Expected 5, got 4
-------- FAIL: TestMaxEmpty (0.00s)
    max_test.go Expected -1, got 0
FAIL
exit status 1
FAIL    github.com/fteem/testing_in_go  0.009s 
```

Enter fullscreen mode Exit fullscreen mode

两项新的测试毫无意外地失败了。如果我们检查这里的输出，我们会注意到每个失败的测试有两行。这两行都以`--- FAIL:`开头，后面是测试函数名。在这一行的最后，还有测试函数运行所花费的时间。

在第二行中，我们看到了测试文件的名称以及失败发生的行号。更具体地说，这是我们两个测试文件中调用`t.Errorf`的地方。

让我们通过测试。首先，我们需要确定`TestMaxInvalid`测试函数中的期望值:

```
func TestMaxInvalid(t *testing.T) {
    actual := Max([]int{1, 2, 3, 4})
    if actual != 4 {
        t.Errorf("Expected %d, got %d", 4, actual)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行它时，我们应该看到少了一个失败:

```
› go test
-------- FAIL: TestMaxEmpty (0.00s)
    max_test.go Expected -1, got 0
FAIL
exit status 1
FAIL    github.com/fteem/testing_in_go  0.006s 
```

Enter fullscreen mode Exit fullscreen mode

很好。我们可以从技术上移除`TestMaxInvalid`，因为它与`TestMax`功能相同。为了使另一个测试通过，当作为参数在`Max`中接收的片为空时，我们需要返回【T2:】

```
package main

func Max(numbers []int) int {
    if len(numbers) == 0 {
        return -1
    }

    var max int

    for _, number := range numbers {
        if number > max {
            max = number
        }
    }

    return max
} 
```

Enter fullscreen mode Exit fullscreen mode

`len`功能将检查`numbers`切片的长度。如果是`0`，就会返回`-1`。让我们再次运行测试:

```
› go test
PASS
ok github.com/fteem/testing_in_go   0.006s 
```

Enter fullscreen mode Exit fullscreen mode

回到通过测试。根据我们的新变化，当参数中的片为空时，`Max`函数将返回`-1`。

## 在关闭

我们在这篇文章中讨论的是什么是测试。我们知道测试是有用的，测试只是代码，仅此而已。我们看到了如何不用任何库或框架，只用简单的 Golang 代码来测试我们自己的代码。

然后，我们继续探索 Golang 的`testing`包。我们看到了实际测试函数的样子。我们讨论了函数定义，我们必须传递的`testing.T`参数以及如何让测试失败。然后我们为我们的`Max`函数添加了更多的测试，并通过了测试。

正如您所看到的，简单地说，测试是一项非常简单但功能强大的技术。通过一点简单的代码，我们可以确保我们的代码在我们可以控制的情况下正常运行。随着任何新功能添加到我们的代码中，我们可以很容易地投入另一个测试，以确保它被覆盖。

显然，我们将在其他文章中探索更多的测试，但是现在我们对这些基本的思想和方法有信心，我们可以在此基础上建立我们的知识。

在我们停止之前，请在评论中让我知道你喜欢和不喜欢测试你的代码的什么？此外，测试中的哪些话题让你感到困惑或具有挑战性？