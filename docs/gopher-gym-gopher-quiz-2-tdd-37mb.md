# 地鼠健身房-地鼠测验 2 - TDD！

> 原文：<https://dev.to/adil_w3nomad/gopher-gym-gopher-quiz-2-tdd-37mb>

2019 年 6 月 2 日 01 时 52 分

* * *

我们以三项任务完成了上一篇文章:

*   添加测试
*   记录分数
*   添加允许自定义 CSV 文件的标志。

当务之急是添加测试，因为我坚信测试驱动开发的好处，通常缩写为 TDD。简而言之，你写一个你想要实现的行为的测试。然后您运行测试，看到它失败，并做尽可能小的改变使它通过。一旦你的测试通过，就该重构了。这样就成了一个循环；红色，绿色，重构。

它帮助您避免了许多编码时常见的陷阱，并迫使您更抽象地考虑您的代码和领域。

Go 有一个来自标准库的`testing`包，所以让我们试着写一个单元测试(单元测试是测试一个小的‘单元’代码的测试)。让我们挑选一小段执行单一功能的代码，并为它编写一个测试。如果你忘记了代码现在是什么样子，下面是全部内容

```
package main

import (
    "bufio"
    "encoding/csv"
    "fmt"
    "log"
    "os"
    "strings"
)

type quizItem struct {
    question string
    answer   string
}

func main() {
    csvFile, err := os.Open("problems.csv")
    if err != nil {
        log.Fatal(err)
    }
    defer csvFile.Close()

    reader := csv.NewReader(csvFile)
    records, err := reader.ReadAll()

    if err != nil {
        log.Fatal(err)
    }

    for i := 0; i < len(records); i++ {
        // Create quizItem object
        quizItem := quizItem{records[i][0], records[i][1]}
        // Print out the question
        fmt.Println("Question:", quizItem.question)
        // Create reader and allow user to input their answer
        reader := bufio.NewReader(os.Stdin)
        fmt.Print("Enter your answer now: ")
        // Expect answer to be given once they hit return
        text, _ := reader.ReadString('\n')
        fmt.Println("Your answer is:", text)
        // Trim the newline suffix from the input
        text = strings.TrimSuffix(text, "\n")
        if text == quizItem.answer {
            fmt.Println("Correct! Well done")
        } else {
            fmt.Println("WRONG! Answer is:", quizItem.answer)
        }
    }

} 
```

让我们回顾一下`main`函数正在做什么。

1.  它读取一个 CSV 文件并将其加载到内存中，直到`main`函数执行完毕(`defer`是一个关键字，允许您在函数执行结束时运行一些代码)
2.  它实例化了一个 CSV 读取器，并使用`reader.ReadAll()`创建一个由字符串组成的切片切片(ReadAll 的成功返回值类型是`[][]string`，它转换为二维数组，其中内部切片中的所有元素都是字符串。
3.  我们创建一个 for 循环，它将遍历最外层切片中的每个**内层**切片(内层切片是 CSV 中的一行，最外层切片是所有 CSV 行)。
4.  我们从包含在**内部**切片中的信息创建并实例化一个`quizItem`结构。
5.  我们打印来自`quizItem`结构的问题，并打印给用户的指令。
6.  我们实例化了一个`bufio`阅读器来接收来自`stdin`的输入，直到它接收到一个`/n`按键。
7.  我们打印用户的答案，然后用`TrimSuffix`去掉用户答案中看不见的`/n`字符。
8.  最后，我们将两者进行比较，并向用户打印一条消息，根据他们得到的答案是对还是错，消息的内容会有所不同。

第 8 点是我想测试的；因此，让我们想象一下在理想世界中，代码会是什么样子。我想让它接受两个参数，都是字符串类型的，我也想让它返回一个字符串。我希望返回值由传入的参数决定，特别是它们是否匹配。

# 2019 年 6 月 3 日 04:05

[秒表计时触发器:主旋律，Spotify 上伦敦爱乐乐团的一首歌](https://open.spotify.com/track/3ymuMYnJtBvcs5xk8MuBRl?si=3yKs1ScWR_yA_3rSFy1RUQ)

让我们在围棋中写一个测试吧！这里有三个步骤

1.  创建一个测试代码单元的函数。测试函数应该具有格式`function TestXxx(*testing.T)`，其中`Xxx`不是以小写字母开头。(星号是指向内存中测试变量位置的指针)
2.  创建一个文件名以`_test.go`结尾的文件，并将我们的测试放在这个文件中。
3.  快跑`go test`看看血(希望如此)！

这很简单，不是吗？让我们开始吧。
`$ touch main_test.go`在我们的本地目录下创建测试文件，现在包含 4 个文件；

— `GopherQuiz`我们用`go build`
— `main.go`编译的二进制可执行文件包含我们的代码
— `main_test.go`我们新创建的测试文件
— `problems.csv`包含我们测验的 CSV 文件

目前，我们将保持这样，但重要的是要认识到，随着项目的增长，组织你的文件是极其重要的。它帮助其他人更快地找到他们正在寻找的东西，同时也为代码提供了上下文。`pool/write.go`很容易被识别为与写入池相关的文件，而`channel/write.go`则与写入通道相关。

话题结束了，让我们回到在`main_test.go`
中编写测试

```
package main

import “testing”

func TestCompareAnswer(t *testing.T) {
  got := CompareAnswer(“3”, “3”)
  if got != true {
  t.Errorf(“CompareAnswer = %v; want true”, got)
 }
} 
```

首先我们指定我们正在测试的包，在这个例子中是`main`。我们导入了`testing`包，这样我们就可以使用它了(记住测试函数的格式需要`t *testing.T`作为参数。然后我们定义我们的测试。

现在，我将继续一个关于测试驱动开发(TDD)的小话题。当进行 TDD 时，**你总是先写一个测试**，在任何其他代码之前。这是什么意思？这意味着在你考虑**如何**做某事之前，你要考虑**想做什么**。

我想比较两个字符串，看它们是否匹配，并根据匹配与否返回不同的字符串。所以我将把我的测试称为`CompareAnswer`。但仅仅是对比答案吗？不，它还会根据比较结果返回成功或失败的消息。所以(不进入命名的兔子洞)，我将把测试的名字改为`CheckAnswer`。

```
func TestCheckAnswer(t *testing.T) {
  got := CheckAnswer(“3”, “3”)
  if got != true {
    t.Errorf(“CheckAnswer = %v; want true”, got)
  }
} 
```

回到代码；然后，我们将测试函数`CheckAnswer`的输出设置为一个名为`got`的变量。以防你忘记，`:=`是声明隐式类型变量的简写。然后我们断言:我们得到的是`true`吗？如果是，使用`t.ErrorF`(打印格式错误)让测试失败，在这里我们让用户知道他们得到了什么，我们期望什么。如果执行时没有失败，测试就通过了。(仅供参考，`ErrorF`就是`LogF`后跟`Fail`。如果我们希望测试失败而不给出消息，我们可以使用`Fail`。)

很简单，对吧？没有点击诱饵，但是这个简单的技巧将会节省你数小时的开发时间；一个好的测试套件是当事情变得糟糕，你需要应用补丁时，讨厌和喜欢你的工作之间的区别。

我离开了我的第一份工作，因为在零测试的情况下开发 web 应用程序所带来的压力；没有什么比不得不修改代码并且不知道是否会破坏其他东西更可怕的了。

好了，回到测试上来。这实际上是一个糟糕的测试，因为尽管它的名字是正确的，但断言是错误的。让我们回想一下；我们希望这段代码做什么？我们希望它比较两个字符串，如果匹配就返回一个字符串，如果不匹配就返回另一个字符串。让我们随着有史以来最好的视频游戏 OST[森林的秘密](https://open.spotify.com/track/4YgdFe3M47sj0ysVQhOF75?si=EQEqLneaSIOzRTcWM5jyog)的甜美声音做出改变。

```
func TestCheckAnswer(t *testing.T) {
  gotCorrect := CheckAnswer(“3”, “3”)
  if gotCorrect != “Correct! Well done” {
    t.Errorf(“CheckAnswer = %v; want ‘Correct! Well Done’”, gotCorrect)
  }
  gotIncorrect := CheckAnswer(“3”, “2”)
  if gotIncorrect != “WRONG! Answer is: 2” {
    t.Errorf(“CheckAnswer = %v; want ‘WRONG! Answer is: 2’”, gotIncorrect)
  }
} 
```

好的，让我们一步一步来。我们将正在测试的函数的返回赋值给`gotCorrect`，将它与我们期望得到的字符串进行比较，如果我们没有得到我们期望的结果，那么就失败。然后我们用一个不正确的答案做同样的事情，重复这个过程。不错！

但是这里有一个小问题。看似无伤大雅、天真无邪的事情。这种东西可以很容易地构成一个测试套件，它是你对付 bug 的盾牌和剑，沉重而笨拙，很快会成为一种负担，让你的开发伙伴诅咒你，并且可以推迟没有经验的开发人员编写测试。

回想一下我们开始编写这个测试的原因；我们想从我们的代码中取出一个**单元**,并测试它是否能独立工作。如果它自己能工作，那么很明显，如果程序不能工作，问题出在程序的另一部分。通过制作小的、模块化的**代码单元**，我们可以独立地测试每个部分，这样一来，可以隐藏 bug 的表面区域就变得更小了。这种小型模块化代码单元的概念，独立完成一件事情，通常被称为**单一责任原则** (SRP)。

我们的测试违反了 SRP 它有两个责任。检查您的代码是否违反 SRP 的一个简单方法是询问它做了什么。

> **我**:最近怎么样`TestCheckAnswer`，过得怎么样？你最近睡得太多了吗？
> 
> 什叶派穆斯林，他们给了我一些奴隶式的东西。
> 
> 我:你什么意思，伙计？我以为你只是在检查答案，这是你该做的，对吗？
> 
> 伙计，看看我该死的名字，我是 T1，但出于某种原因，我必须检查两个答案。当我想去度假时会发生什么？你们不会有人测试`CheckAnswer`！事实上，我甚至不想被称为`TestCheckAnswer`，我想被称为`TestCheckCorrectAnswer`，这样我就不用在这里做两个人的工作了！帮我一个忙，尽快改变，因为`CheckAnswer`可能会开始有更多的工作要做，然后你们会让我做更多的工作，因此**你**也要做更多的工作！来吧，兄弟，在这里和我一起工作，我不想像`main`一样结束，那家伙有这么多问题，我甚至不知道他怎么还在！

(我总是以我的一个非常懒惰的好朋友为基础，将我的测试拟人化。自然，他是一个伟大的程序员！)

测试是功能，功能应该有一个单一的职责，因此，我们应该改变这个测试来有一个单一的职责。我们通过将它们分开来实现，这在这个例子中非常简单；

```
package main

import “testing”

func TestCheckCorrectAnswer(t *testing.T) {
  got := CheckAnswer(“3”, “3”)
  if got != “Correct! Well done” {
    t.Errorf(“CheckCorrectAnswer = %v; want ‘Correct! Well Done’”, got)
  }
}

func TestCheckIncorrectAnswer(t *testing.T) {
  got := CheckAnswer(“3”, “2”)
  if got != “WRONG! Answer is: 2” {
    t.Errorf(“CheckIncorrectAnswer = %v; want ‘WRONG! Answer is: 2’”, got)
  }
} 
```

这样做的好处是，它使我们的代码更加灵活；灵活的代码很好，因为未来是不确定的，面对不确定性，我们必须能够快速适应新的条件。

对！我们已经得到了我们的测试，现在呢？我们运行它，看着它失败！

```
06:24 $ go test
# github.com/adilw3nomad/GopherQuiz [github.com/adilw3nomad/GopherQuiz.test]
./main_test.go:6:9: undefined: CheckAnswer
./main_test.go:13:9: undefined: CheckAnswer
FAIL    github.com/adilw3nomad/GopherQuiz [build failed] 
```

现在是有趣的部分。我们编写最少、最简单的代码来改变错误消息。把测试错误输出想象成你的提示指南；它告诉我们`CheckAnswer`是未定义的，我们来定义它吧！

```
func CheckAnswer() {
} 
```

就是这样！我们尽可能做出最小的改变来满足我们收到的错误。现在我们再次运行测试。

```
06:29 $ go test
# github.com/adilw3nomad/GopherQuiz [github.com/adilw3nomad/GopherQuiz.test]
./main_test.go:6:20: too many arguments in call to CheckAnswer
        have (string, string)
        want ()
./main_test.go:6:20: CheckAnswer("3", "3") used as value
./main_test.go:13:20: too many arguments in call to CheckAnswer
        have (string, string)
        want ()
./main_test.go:13:20: CheckAnswer("3", "2") used as value
FAIL    github.com/adilw3nomad/GopherQuiz [build failed] 
```

成功！我们得到了不同的错误消息。测试中不同的错误信息总是一件好事；意味着你在进步。这个有点不一样；它告诉我们`TestCheckCorrectAnswer`向`CheckAnswer`发送了太多的参数。它甚至通过告诉我们已经给了它什么`have (string, string)`，以及它想要什么`want ()`来帮助我们。

记住，我们希望**满足**的测试。所以如果测试有两个字符串要通过，我们必须让`CheckAnswer`接受两个字符串。

```
func CheckAnswer(string, string) {
  return
} 
```

再次运行测试给我们一个更奇特的输出；

```
06:35 $ go test
# github.com/adilw3nomad/GopherQuiz [github.com/adilw3nomad/GopherQuiz.test]
./main_test.go:6:20: CheckAnswer("3", "3") used as value
./main_test.go:13:20: CheckAnswer("3", "2") used as value
FAIL    github.com/adilw3nomad/GopherQuiz [build failed] 
```

这个错误消息有点不同，但是仍然很有用！它告诉我们，我们进行的函数调用是作为一个值使用的。这是什么意思？

如果你看一下上面的`CheckAnswer`函数，你会发现我们没有指定返回类型。这意味着函数本身被返回。这很酷，我想知道这是否意味着你可以创建返回其他函数的函数？

不管怎样，为了解决这个问题，我们添加了一个返回值。

```
func CheckAnswer(string, string) int {
  return 1
} 
```

```
06:36 $ go test
# github.com/adilw3nomad/GopherQuiz [github.com/adilw3nomad/GopherQuiz.test]
./main_test.go:7:9: cannot convert "Correct! Well done" (type untyped string) to type int
./main_test.go:7:9: invalid operation: got != "Correct! Well done" (mismatched types int and string)
./main_test.go:14:9: cannot convert "WRONG! Answer is: 2" (type untyped string) to type int
./main_test.go:14:9: invalid operation: got != "WRONG! Answer is: 2" (mismatched types int and string)
FAIL    github.com/adilw3nomad/GopherQuiz [build failed] 
```

我用`int`向你展示了，尽管我们知道我们的函数将做什么(因为我们加入了它)，但是有很多场景你不知道从哪里开始。在这些情况下，最好的办法是做一个假设，然后运行测试看看会发生什么。在这种情况下，这很容易解决。

```
func CheckAnswer(string, string) string {
  return ""
} 
```

```
06:47 $ go test
--- FAIL: TestCheckCorrectAnswer (0.00s)
    main_test.go:8: CheckCorrectAnswer = ; want 'Correct! Well Done'
--- FAIL: TestCheckIncorrectAnswer (0.00s)
    main_test.go:15: CheckIncorrectAnswer = ; want 'WRONG! Answer is: 2'
FAIL
exit status 1
FAIL    github.com/adilw3nomad/GopherQuiz       0.005s 
```

我们越来越近了！让我们尽可能做最简单的事情来通过其中一个测试。

```
func CheckAnswer(string, string) string {
  return "Correct! Well done"
} 
```

```
06:51 $ go test
--- FAIL: TestCheckIncorrectAnswer (0.00s)
    main_test.go:15: CheckIncorrectAnswer = Correct! Well done; want 'WRONG! Answer is: 2'
FAIL
exit status 1
FAIL    github.com/adilw3nomad/GopherQuiz       0.005s 
```

终于！通过测试！但是我们还没有完成，因为另一个测试仍然失败。但这是 TDD 的本质；您以增量方式编写小的更改，并在每次进行时运行您的测试。

当你遇到一个错误消息时，你对它的原因做一个假设，对你的代码做相关的修改，并通过…运行测试！这样，您就不会添加不需要的代码。

我现在要跳到前面，向您展示最终的`CheckAnswer`函数。

```
func CheckAnswer(answer string, correctAnswer string) string {
  if answer == correctAnswer {
    return “Correct! Well done”
  } else {
    return (“WRONG! Answer is: “ + correctAnswer)
  }
} 
```

```
06:56 $ go test PASS
ok      github.com/adilw3nomad/GopherQuiz       0.004s 
```

我们做到了！不再有失败的测试！

我希望你喜欢这篇博文，并发现它信息丰富，一如既往，C&C 受到鼓励和赞赏。我将把它留在这里，因为它变得相当长，但在下一篇博文中，我们将继续这项任务。

*   添加可定制的定时器
*   实施分数
*   允许通过文件名传递自定义测验