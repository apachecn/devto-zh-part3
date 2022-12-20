# 理解 Swift 闭包——第 1 部分

> 原文：<https://dev.to/ruffle1986/understanding-swift-closures---part-1-390m>

> “任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”马丁·福勒

由于我是由[保罗·哈德森](https://www.hackingwithswift.com/about)发起的[100 天 Swift](https://twitter.com/hashtag/100DaysOfSwift) 挑战赛的忠实参与者，在某些时候遇到关闭是不可避免的。尽管我对其他语言中的闭包(或 lambdas)以及它们为什么有用有着深刻的理解，但我意识到闭包是我在阅读别人的 Swift 代码时感到吃力的主要原因之一。

第 7 天之后，我不想就这么算了，所以我决定深入挖掘。我写这篇文章的原因是我想收集我的发现，巩固我在旅途中获得的知识。希望你会发现它是有益的，并接受与你相关的价值。

好吧，我们开始吧。

# 什么是闭包？

根据官方文档，“闭包是自包含的功能块，可以在代码中传递和使用”。这同样适用于函数，所以如果我说它们基本上是函数的话，那就差得不远了，但是你可以用一些不同于普通函数的方式来编写和使用它们，希望能有更好的代码质量。

# 一等物体

在编程中，“一级”这个词的意思是，你可以把这些所谓的一级对象赋给变量，作为函数参数传递，作为函数执行的结果返回。这与 Swift 中的功能没有什么不同。在你认为必须使用闭包语法来将函数赋给变量或将其作为函数参数传递之前，我必须指出这是不正确的。选择哪个完全取决于你，普通函数还是闭包。在我看来，一个并不比另一个更好，这取决于什么最适合你。如果使用普通函数更容易理解你的代码，那就使用它。但是不用说，掌握关于闭包的知识是非常重要的，尤其是如果你不是唯一一个正在从事某个项目的开发人员，或者你即将在你梦想的公司经历一个招聘过程。

# 函数类型标注

“所以我可以像处理字符串、整数或其他类型一样处理函数。到目前为止一切顺利，但我为什么要这么做？”在进入实用部分之前，让我们看看如何在 Swift 中编写普通的函数和闭包。

由于 Swift 是一种强类型语言，您必须在某种程度上向变量添加类型注释。幸运的是，Swift 足够聪明，可以通过您赋予变量的值来判断变量的类型。这就是所谓的类型推断。但是我们现在先超级显式，以后再优化。

在下面的例子中，有一个名为`countLetters`的普通函数，它接受一个字符串并返回一个整数。

```
func countLetters(word: String) -> Int {
    return word.count
}

let stillCountLetters: (String) -> Int = countLetters 
```

Enter fullscreen mode Exit fullscreen mode

函数`countLetters`被分配给一个新变量`stillCountLetters`。正如我们所同意的，让我们明确设置`stillCountLetters`的类型为`(String) -> Int`。这意味着“嘿，我可以存储一个指向函数的指针，该函数接受一个字符串`(String)`并返回一个整数`-> Int`”。如果一个函数的 arity 为零，这意味着它不接受任何东西，你可以写一个左括号和一个右括号，在这两个括号之间什么也不写。如果你的函数不返回任何东西，你可以在`->`后使用`Void`类型。

所以简而言之，当一个函数不接受任何东西也不返回任何东西时，这个函数的类型看起来是这样的:

```
let myFunc: () -> Void 
```

Enter fullscreen mode Exit fullscreen mode

在函数声明处，只有当函数不返回任何内容时，才可以省略返回值的类型。这将是一个隐含的`Void` :

```
func printMessage(message: String) {
    print(message)
} 
```

Enter fullscreen mode Exit fullscreen mode

除了`Void`，你还可以使用圆括号，但是对我来说，圆括号太多了，乍一看，它让我和元组混淆了，当然这是没有意义的，但是仍然:

```
let myFunc: () -> ()

func myFunc2() -> () {
} 
```

Enter fullscreen mode Exit fullscreen mode

# 闭包语法

好了，在上一节中，我们创建了一个普通函数，并把它赋给了一个变量。是时候看看我们如何编写闭包了。

```
let countLetters: (String) -> Int = { (word: String) -> Int in
    return word.count
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，该类型与普通函数的类型相同。花括号内的所有内容都是属于闭包的功能。在`in`关键字之前，可以找到传入值`(word: String)`和返回值的类型`-> Int`。`in`之后的一切都是你的函数做的。在正常函数的情况下，你通常会把它放在括号里。

# 为什么封号？

此时，您可能会说“好吧，这是闭包语法，好吧！这和普通的函数有点不同。你为什么要迷惑我，苹果？”。是的，我听到了。这就像“制表符对空格”的战争，不是吗？嗯，事情没那么简单，苹果在 Swift 中引入 closures 有明显的原因。

闭包概念背后的主要原因是方便。它提供了一种使用函数的便捷方式。这有点像遵循“少写多做”的原则。刚开始的时候，当你的代码库只有几行代码时，这似乎没有什么不同。但是后来当你准备扩展你的应用程序时，你添加了一些新的特性，错误开始出现，你最终会维护一个庞大的不容易调试的东西。更不用说如果你不得不在项目中与其他开发人员一起工作，甚至更糟，你已经继承了一个巨大的遗留代码库。

当涉及到创建和维护一个可伸缩和可测试的应用程序时，这是很容易理解的，最近为了编写满足上述需求的代码，遵循函数式编程范例是很常见的。长话短说，如果 FP 是你的东西，你非常依赖函数，Swift 提供了一种更友好的方式来处理它们。句号。

在接下来的几节中，我将介绍闭包在将来可能会派上用场的情况，或者如果您决定追随函数式编程潮流的话。

# 省略标签

我最喜欢的一点是，当你用参数调用闭包时，不需要使用标签。而且，它甚至根本不允许你使用它们。以下代码将在编译时失败:

```
let countLetters = { (word: String) -> Int in 
    return word.count
}

countLetters(word: "Raptors") // error: extraneous argument label 'word:' in call ... 
```

Enter fullscreen mode Exit fullscreen mode

您也不能覆盖外部使用的标签:

```
let countLetters = { (of word: String) -> Int in 
    return word.count
}

countLetters(of: "Raptors") // error: closure cannot have keyword arguments 
```

Enter fullscreen mode Exit fullscreen mode

不要误解我。我看到并理解标签的好处，在阅读了罗伯特·马丁的几篇文章后，我可能会觉得我想在任何地方都使用它们。但是我还不习惯标签，当我调用函数时，我总是忘记写标签，而且它们不是可选的。

# 少写，多做

本节描述了 Swift 中的一些可用功能，这些功能有助于您编写更短的代码以提高可读性，但如果使用不当，也会很危险。

***在适当的位置创建闭包***

正如我前面说过的，函数是一等公民，这意味着你可以将它们作为函数参数传递。它的普通功能和闭包都是负担得起的。假设你有一个函数来`add`两个数字，另一个函数叫做`combine`，它接受一个函数，你可以用这个函数以某种方式组合这两个数字。现在，不要试图理解将函数传递给其他函数有什么意义。稍后我会描述它。

```
func add(a: Int, b: Int) -> Int {
    return a + b
}

func combine(a: Int, b: Int, by function: (Int, Int) -> Int) -> Int {
    return function(a, b)
}

let result = combine(a: 2, b: 3, by: add)

print(result) // 5 
```

Enter fullscreen mode Exit fullscreen mode

这样做完全没问题。我们在这里非常明确，但它仍然有效。

有了闭包，你可以稍微缩短上面的代码:

```
func combine(a: Int, b: Int, by function: (Int, Int) -> Int) -> Int {
    return function(a, b);
}

let result = combine(a: 2, b: 3, by: { (a: Int, b: Int) -> Int in 
    return a + b
})

print(result) // 5 
```

Enter fullscreen mode Exit fullscreen mode

不能在调用处创建普通函数。通过在我们的例子中使用闭包，我们可以节省 3 行代码。这并不多，但是想象一下一个有数百行代码的更大的模块。通过指定我们的`combine`函数应该如何计算，我们提高了可读性，这样开发人员就不必跳到代码库中的另一个地方去查看`add`在研究我们的代码时做了什么。通过这样做，我们可以避免“变化盲目”:

> 变化盲是一种感知现象，当视觉刺激发生变化而观察者没有注意到时，就会出现这种现象。例如，当图像忽明忽暗时，观察者往往注意不到图像中的主要差异。人们察觉变化的能力差被认为反映了人类注意力的基本局限性。

<small>来自[维基百科](https://en.wikipedia.org/wiki/Change_blindness)T3】</small>

我们可以保持心流状态:

> 在积极心理学中,“心流状态”,俗称“入定”,是一种精神状态，在这种状态下，从事某项活动的人完全沉浸在一种精力充沛的专注感中，完全投入并享受活动过程。

<small>来自[维基百科](https://en.wikipedia.org/wiki/Flow_(psychology))T3】</small>

***式推理的力量***

正如我之前提到的，如果你一致地编写代码，Swift 足够聪明，可以根据类型推断自动计算类型。考虑到这一点，我们可以做进一步的改进。

因为在`combine`函数中已经声明了`function`参数的返回类型必须是整数，所以当我们传递它时，我们可以省略闭包返回值的类型:

```
let result = combine(a: 2, b: 3, by: { (a: Int, b: Int) in 
    return a + b
}) 
```

Enter fullscreen mode Exit fullscreen mode

这同样适用于参数:

```
let result = combine(a: 2, b: 3, by: { (a, b) in 
    return a + b
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你真的想的话，代码很短，一行就能写完。但我个人更喜欢垂直传播而不是水平传播:

```
let result = combine(a: 2, b: 3, by: { (a, b) in return a + b }) 
```

Enter fullscreen mode Exit fullscreen mode

***隐式返回***

然而，我们可以将代码的长度减少到 1 行 64 个字符(在放入下一行之前，推荐的最大长度是 80)，就可读性而言，这仍然不是最好的。我们可以进一步利用闭包内部的隐式返回。如果闭包的主体只包含一个语句，并且您想要返回它的结果，那么您可以省略`return`关键字。

```
let result = combine(a: 2, b: 3, by: { (a, b) in a + b }) 
```

Enter fullscreen mode Exit fullscreen mode

***速记论据***

Swift 甚至允许您省略参数并使用它们的简写版本。速记语法通过使用参数列表中从零开始的索引来指向一个项目。你必须在索引前加上美元符号。所以在我们的例子中，`a`和`b`的简写版本是`$0`和`$1`。通过使用速记参数，你也可以省略`in`关键字:

```
let result = combine(a: 2, b: 3, by: { $0 + $1 }) 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我认为是时候指出这一点了，然而，这是一个非常奇特的解决问题的方法，它真的伤害了可读性。如果我们只是在做实验或者只是在黑东西，那没问题。公平地说，即使我是一个经验不足的 Swift 开发人员，我也可以认为这个闭包增加了两个数字。但是不要忘记，我们可以用`+`操作符将两个字符串连接在一起。乍一看，不一定清楚这两个参数的类型。如果 XCode 是你编写 Swift 的首选编辑器，工具提示就能派上用场。当决定是否应该使用速记论证时，特别是如果它是一个非常复杂的功能，请三思，想想你的同事和未来的自己。

***操作方法***

还是不够？！啊，你要杀了我！好吧！如果闭包内只有一个语句，并且是基本的算术运算或者将一个值与另一个值进行比较，那么只需要编写操作符就足够了，Swift 会为您完成其余的工作:

```
let result = combine(a: 2, b: 3, by: +) 
```

Enter fullscreen mode Exit fullscreen mode

奖金:

你知道你甚至不需要写任何东西吗？？？Swift 可以读懂你的想法，所以下面的代码确实很管用:

```
let result = combine(a: 2, b: 3, by:) 
```

Enter fullscreen mode Exit fullscreen mode

开玩笑的。并没有。抱歉，我的幽默感很糟糕。:(

# 尾随闭包

万岁，我们终于到了实用的部分。那么为什么要使用闭包呢？嗯，这是一个非常好的问题，但最好问问自己，为什么要使用函数式编程。即使你没有意识到这一点，如果你是一名全职的 Swift 开发人员，你可能会经常使用 FP。

假设我们有以下观点。当视图出现在屏幕上时，它会显示一个带有问候语的弹出窗口。让我们仔细看看`viewDidAppear`函数的内部，弹出窗口是在这个函数中创建并被告知要显示的。它创建一个警报视图和一个操作，将该操作添加到警报中，然后打开弹出窗口。

```
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    func alertActionHandler(_ action: UIAlertAction) {
        print("Great!")
    }

    func alertCompletionHandler() {
        print("Completion feels good.");
    }

    override func viewDidAppear(_ animated: Bool) {

        let alert = UIAlertController(title: "Greeting", message: "Hello, World!", preferredStyle: .alert)
        let action = UIAlertAction(title: "OK", style: .default, handler: alertActionHandler)

        alert.addAction(action)

        self.present(alert, animated: true, completion: alertCompletionHandler)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`UIAlertAction`类的第三个参数接受一个名为`handler`的闭包。就像“好的，开始！我给你这个功能。你能不能行行好，在用户点击动作按钮的时候帮我叫一下？”。
我 100%确定，你已经遇到过这类事情，是的，这是函数式编程。当你将一个函数传递给另一个函数，等待某个事件发生时(在我们的例子中是用户点击 OK 按钮)，这就是 NodeJS 等其他语言中所谓的回调设计模式。它在 Swift 中可能有不同的名称，但概念是相同的。如果你不想让你的应用程序在点击事件之前停止，这是非常强大的。

正如我前面所说的，使用闭包是可选的。这只是使用回调的一种便捷方式。如果你不喜欢这个想法，你可以保持正常的功能。在上面的例子中，我将`alertActionHandler`回调声明为`ViewController`类的成员，并将其传递给`UIAlertAction`。顺便提一下，您也可以将`viewDidAppear`方法中的函数声明为嵌套函数。如果您想从父上下文中访问变量，这是非常强大的，但是稍后会详细介绍。

这是一个很好的例子来展示闭包的威力，更准确地说是尾随闭包。让我们重构这段代码，看看用闭包替换类方法后是什么感觉。让我们只是不要使用尾随结束拳头。

```
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    func alertCompletionHandler() {
        print("Completion feels good.");
    }

    override func viewDidAppear(_ animated: Bool) {

        let alert = UIAlertController(title: "Greeting", message: "Hello, World!", preferredStyle: .alert)
        let action = UIAlertAction(title: "OK", style: .default, handler: { _ in
            print("Great!")
        })

        alert.addAction(action)

        self.present(alert, animated: true, completion: alertCompletionHandler)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这要好得多，因为它不会把用户从流中抢出来，因为我们已经声明了回调，所以她不必向上滚动到类方法。尾随闭包语法的承诺是可读性更好，所以让我们看看它是什么样子的。为了帮助您关注相关的部分，我已经删除了剩余的代码:

```
 let action = UIAlertAction(title: "OK", style: .default) { _ in
        print("Great!")
    } 
```

Enter fullscreen mode Exit fullscreen mode

事情是这样的。当且仅当给定函数的最后一个参数是闭包时，才允许使用尾随闭包语法。如您所见，通过使用它，您可以省略标签。我让你来决定与前面的例子相比是否更容易阅读。如果你喜欢更明确和使用标签，这完全取决于你。

您可能已经注意到，我在代码中的另一个地方应用了回调模式。该类的`present`方法接受闭包作为最后一个参数，因此我们可以再次利用尾随闭包语法来使代码更短一些。

```
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    override func viewDidAppear(_ animated: Bool) {

        let alert = UIAlertController(title: "Greeting", message: "Hello, World!", preferredStyle: .alert)
        let action = UIAlertAction(title: "OK", style: .default) { _ in
            print("Great!")
        }

        alert.addAction(action)

        self.present(alert, animated: true) {
            print("Completion feels good.");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 捕捉数值

也可以从其他函数中返回一个函数。记得吗？当你在一个函数中声明另一个函数时，它就是一个嵌套函数。如果您希望基于某些输入在父上下文(即创建嵌套函数的函数)中存储一些状态，这可能非常有用。嵌套函数可以访问父上下文中的变量。当你调用返回一个函数的函数时，就像“好的，我做了你想让我做的，如果你想进一步做其他事情，这里有一个函数(基于存储的状态)。”

在下面的代码中，有一个`addUser`函数，您可以用它向数据库添加一个用户:

```
var users = Set<String>()

func addUser(_ user: String) -> () -> Void {

    users.insert(user)

    func removeUser() {
        users.remove(user)
    }

    return removeUser
}

let removeJohn = addUser("John")

print(users) // ["John"]

removeJohn()

print(users) // [] 
```

Enter fullscreen mode Exit fullscreen mode

这是一个有点傻的例子，但是足够简单明了。`addUser`负责将给定的用户名添加到`users`集合中。它还返回一个函数，您可以使用该函数删除刚刚添加的用户。你不需要记住`addUser`是如何工作的。它在后台使用什么集合类型并不重要。如果您想添加 John，只需调用输入为“John”的函数，存储返回的 remove 函数，该函数属于父上下文，John 作为新用户存储在该上下文中。

我们可以说`addUser`是全局上下文中的嵌套函数。因此，即使我在函数外部声明了`users`集合，它也可以“捕获”该变量。

让我们看看闭包是什么样子的:

```
var users = Set<String>()

func addUser(_ user: String) -> () -> Void {

    users.insert(user)

    return {
        users.remove(user)
    }
}

let removeJohn = addUser("John")

print(users)

removeJohn()

print(users) 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在就这些。我希望您对闭包有更好的理解，并发现它们很有用。如果没有，那也可以。归根结底，重要的是你的应用程序是否有效。下次我将向您展示 Swift 中闭包的其他肮脏秘密。如果我错过了什么重要的东西或者我错了，请在评论区告诉我。

保重！