# 地鼠健身房:问答游戏-第一部分

> 原文：<https://dev.to/adil_w3nomad/gopher-gym-quiz-game-part-1-4lbo>

# 2019 年 5 月 29 日 05 时 20 分

我用一些牛仔咖啡开始了我的一天；那是在平底锅里煮的咖啡，因为我似乎把我的 aero press 放错了地方，因为我听了 [Go for 初学者](https://open.spotify.com/episode/6TL5HwIMLVuSVQCUt2sLMc?si=xs4s3t2TS12QCrlWhKdbrQ)，发现了 [Gophercises](https://gophercises.com) ，这是一套针对那些完成了一些教程但有点失去动力的人的 Go 编程练习。我觉得这就是我，所以我决定在这个博客中记录我的进步和学习的同时解决这些练习。

/ *我有一种独特的写作风格，因为我是和编码一起写的，所以有很多时态转换，所以提前道歉。所有的评论和批评都是被鼓励的，不管有多刺耳或刺耳，只要是有建设性的！* /

我将处理第一个挑战，[GitHub-gophercises/quiz:Ex 1-通过命令行运行定时测验](https://github.com/gophercises/quiz)。

所以我要做的第一件事就是做一个新的围棋项目。我在 github 上创建了一个新的存储库，将其克隆到我的`$GOPATH/src/github.com/adilw3nomad`中，并创建了一个简单的`main.go`文件，在运行时打印`Hello!`。

我运行`go build && ./GopherQuiz`，它将我的程序编译成一个名为`GopherQuiz`的二进制文件，并运行这个文件。`Hello!`被打印到命令行。成功！我现在有工作要做了。

让我们复习练习的细节；我将用粗体表示我认为对该领域重要的观点。

> 创建一个程序，该程序将读取通过 ***CSV 文件*** 提供的测验(下面有更多细节)，然后将测验给用户 ***跟踪*** 他们得到多少 ***问题*** 正确*以及他们得到多少 ***错误*** 。不管答案是对是错，下一个问题应该是 ***紧接着*** 问。
> CSV 文件应该默认为`problems.csv`(示例如下所示)，但是用户应该能够通过标志 自定义文件名。*

 *我喜欢把这样的东西分解成要点；

*   有个小测验，里面有问题。
*   问题可能是对的，也可能是错的，因此它们都有答案。
*   有一个分数，或者至少是分数的概念
*   测验是从包含问题和答案的 CSV 文件创建的

这是应用程序的领域；现在让我们考虑一下这种行为。该应用程序具有单一依赖项；它需要一个 CSV 文件来创建测验。没有 CSV 文件就没有小考，app 也没用。因此，我们需要做的第一件事是让我们的应用程序加载和读取 CSV 文件。我不会担心文件名标志，甚至 CSV 的内容。我想关注的是将 CSV 文件加载和读取到应用程序中。

1.  创建数组/切片以保存测验项目。`(Question, Answer)`
2.  阅读 CSV 的每一行，并从中创建一个测验项目。
3.  遍历切片，将问题输出到 stdout
4.  接受用户的回答
5.  如果答案与用户的输入相匹配，则分数计数器加 1。

我要用最糟糕的方式来做这件事。没有测试，全部在`main`函数中。然后我会为它写一些测试，然后我会重构，知道我的 coce 仍然工作多亏了这些测试。

# 2019 年 5 月 31 日 06 时 36 分

```
func main() {
  csvFile, err := os.Open(“problems.csv”)
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
    fmt.Println("Question: ", records[i][0])
    fmt.Println("Answer: ", records[i][1])
  }
} 
```

这就是我目前所知道的。我使用`os`包打开文件，直到程序停止运行才关闭它。然后，我使用`csv.Reader.ReadAll`为每个 CSV 元素创建一个二维数组。最后，为了查看我正在处理的内容，我遍历记录并打印每一条记录。protip`PrintLn`对于打印任何可以打印的东西都很有用。

现在我有了 CSV 输入，我想用它做什么呢？我想

*   询问每个问题
*   接受用户的回答
*   将其与正确答案进行比较
*   如果它们相同，则增加一个计数器。

在命令行 app 中提问是什么意思？这意味着写入 stdout。接受一个答案意味着什么？从 stdin 中读取。我已经通过使用`PrintLn`覆盖了对 stdout 的写入，为了从 stdin 读取，我将使用 [bufio](https://golang.org/pkg/bufio/) 包。

```
// omitted unchanged code for brevity
for I := 0; I < len(records); I++ {
  // Print the question out
  fmt.Println(“Question: “, records[I][0])
  // Read from stdin for answer
  reader := bufio.NewReader(os.Stdin)
  fmt.Print(“Enter your answer now: “)
  // Expect answer to be given once they hit return
  text, _ := reader.ReadString(‘\n’)

  if text == records[I][1] {
    fmt.Println(“Correct!”)
  } else {
    fmt.Println(“WRONG! Answer is: “, records[I][1])
  }
} 
```

我们在这里所做的是遍历 CSV 的行，打印出问题，然后实例化一个新的使用`os.Stdin`接口的缓冲 I/O 读取器。接下来，我们向用户打印一条指令，并使用`reader.ReadString`方法读取他们的输入。传递给`ReadString`的参数是分隔符，它让读者知道何时停止阅读。然后一个简单的条件语句检查答案是否正确。

很可爱，只是它不起作用！我怀疑这是因为我没有做出正确的断言。我们来做一些调试吧！。但首先，我要休息一下，玩一局:D 自动车

# 2019 年 5 月 31 日 08 时 22 分

一场 autochess 游戏(和一些洗衣时间)后，我回来了，准备好了。所以，首先我假设为什么它不能识别正确的答案；我假设我比较的变量类型(`text`和`records[I][1]`)是不同的。让我们通过打印来找出它们的类型。

我们知道(感谢 vscode 的 Go 包)，`reader.ReadString`返回一个字符串。`csv.Reader.ReadAll`返回什么？带字符串的的二维切片。嗯。使用这一小段讨厌的代码，我可以确认它们是同一类型的。那到底是怎么回事！

```
fmt.Println(fmt.Sprintf(“%T”, text)) // string
fmt.Println(fmt.Sprintf(“%T”, records[I][1])) // string 
```

新假设的时间到了；但首先，要做一些重构。我发现理解我写的东西有点困难，主要是因为这些可怕的名字，比如`records[I][1]`。
我要把这些记录变成它们本来的样子；测验中的项目。你说，怎么做？通过构造一个结构！

Go 的结构是字段的类型集合。它们对于将数据组合在一起形成记录很有用，如果你想在 Go 中进行 OOP，它们是非常必要的。Go 不是一种纯面向对象的语言，但是通过方法和结构，你可以编写面向对象的代码。

下面是如何在 Go 中定义一个结构体，这个结构体被恰当地命名为`quizItem`

```
type quizItem struct {
  question string
  answer   string
} 
```

我们从关键字`type`开始，因为 Go 是静态类型的，我们正在定义一种新类型的变量。接下来是结构的名字，`quizItem`，最后是关键字`struct`。在花括号的主体中，我们定义了结构属性，后面是它们的类型。

现在我们已经添加了一个结构
，下面是测试循环的样子

```
/// omitted unchanged code for brevity
for I := 0; I < len(records); I++ {
  // Create quizItem object
  quizItem := quizItem{records[I][0], records[I][1]}
  // Print out the question
  fmt.Println(“Question: “, quizItem.question)
  // Create reader and allow user to input their answer
  reader := bufio.NewReader(os.Stdin)
  fmt.Print(“Enter your answer now: “)
  // Expect answer to be given once they hit return
  text, _ := reader.ReadString(‘\n’)

  if text == quizItem.answer {
    fmt.Println(“Correct!”)
  } else {
    fmt.Println(“WRONG! Answer is: “, quizItem.answer)
  }
} 
```

正如您在第 3 行看到的，实例化该结构非常简单，现在代码可读性更好了。现在它更容易阅读，是时候做一个新的假设，为什么答案比较不起作用。我假设，也许我在假设`text`的值会是多少。

我们打印出来检查一下吧！我将在运行比较检查之前打印用户的答案，所以现在代码看起来像这样

```
/// omitted unchanged code for brevity
for I := 0; I < len(records); I++ {
  // Create quizItem object
  quizItem := quizItem{records[I][0], records[I][1]}
  // Print out the question
  fmt.Println(“Question:”, quizItem.question)
  // Create reader and allow user to input their answer
  reader := bufio.NewReader(os.Stdin)
  fmt.Print(“Enter your answer now: “)
  // Expect answer to be given once they hit return
  text, _ := reader.ReadString(‘\n’)
  fmt.Println(“Your answer is:”, text)

  if text == quizItem.answer {
    fmt.Println(“Correct!”)
  } else {
    fmt.Println(“WRONG! Answer is:”, quizItem.answer)
  }
} 
```

```
$ go build && ./GopherQuiz
Question: 5+5
Enter your answer now:10

Your answer is: 10
WRONG! Answer is: 10
Question: 7+3
Enter your answer now: 
```

这里有些奇怪；打印完用户的答案后会有新的一行。让我们通过查看字符串的 base16 值来研究它。我们可以通过使用`fmt.Printf(“%x”, text)`来做到这一点，其中`%x`是一个格式修饰符，表示我希望字符串被格式化为 base16。让我们编写一些代码来测试这一点；

```
fmt.Println("Your answer is:", text)
fmt.Printf("Your answer in base16 is %x \n", text)
fmt.Printf("The expected answer in base16 is %x \n", quizItem.answer) 
```

```
$ go build && ./GopherQuiz
Question: 5+5
Enter your answer now:10
Your answer is: 10

Your answer in base16 is 31300a
The expected answer in base16 is 3130 
```

啊哈！我的怀疑得到了证实；这里/ *是* /两者的一个区别。用户输入以`0a`结尾，这是换行的十六进制代码。

这里发生的情况是，当从 stdin 中读取时，我们将所有内容都取至 ***并包括*** `\n`，这就是为什么在打印用户输入后会有一个新行的原因。

既然我们知道了这个问题，我们该如何解决它呢？最简单的方法是在比较之前从输入中删除换行符。让我们使用`strings`包，它是一个操作字符串的工具库。快速测试:

```
text = strings.TrimSuffix(text, “\n”)
fmt.Printf(“Your answer trimmed in base16 is %x \n”, text)
fmt.Printf(“The expected answer in base16 is %x \n”, quizItem.answer) 
```

```
$ go build && ./GopherQuiz
Question: 5+5
Enter your answer now:10
Your answer is: 10

Your answer trimmed in base16 is 3130
The expected answer in base16 is 3130 
```

成功！所以我们在这里做的是使用`TrimSuffix`方法，传递给它我们想要应用修剪的文本，以及我们想要修剪的后缀。有了这个，测验应该可以进行了！这里有一个整个程序的快速提示；

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

这就是它实际运行的样子！

所以接下来我们需要做的是:

*   记录分数，并输出正确/错误的分数
*   添加允许自定义 CSV 文件的标志。

但是在我们继续之前，我们必须进行测试！进行测试有助于你理解你的代码，节省你手动测试的时间，并且在一切正常的时候给你一点刺激。

如果你想问我问题或有任何建议，请在 twitter 上关注我。*