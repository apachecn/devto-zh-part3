# 这是写结束语的时候了

> 原文：<https://dev.to/quii/tis-the-season-to-write-clojure-jb9>

`[:fa (take 9 (repeat :la))]`

这篇文章是针对那些想了解 Clojure 的初学者的，这是一种超级有趣的语言，可能与你所熟悉的完全不同。

您将学习一些基本概念，并了解如何编写一个秘密的圣诞老人分配器，因为今天是圣诞节！！！！！:D

## 假设

*   你对编程有些熟悉，理解像流量控制(`if`、`when`、`for`等)、变量、函数、&方法等概念。如果你学过一点 Ruby 或 Javascript，你可能会做得很好。
*   你为这篇文章所做的一切都将通过终端来完成，所以假设你对它有一些熟悉和信心。

## clo jure 是什么？

*   像 Java、Ruby 等通用语言
*   也可以编译成 Javascript，这样你就可以使用 Clojure 为你的网站以及后端编写客户端代码；所以你可以用一种语言编写一个丰富的端到端的网站

## 入门

这里有一个链接，可以安装一个叫 Leiningen 的工具，它可以让你轻松地创建 Clojure 项目。

如果你在 Mac 上并且安装了`homebrew`，你可以运行`brew install leiningen`。如果你没有安装自制软件，[你应该安装它](https://brew.sh/)，因为大多数有用的编码工具都是用它安装的。

通过在命令行中键入`lein`来检查您的安装是否正常，您应该会看到大量的简介。

*第一次运行`lein`时，可能需要下载一些它需要的东西。后续运行不会那么慢。*

#### 如果你看到它在抱怨 JDK

Clojure 是使用 Java 开发工具包编译的，您的计算机上可能没有安装该工具包。在 Mac 上你可以使用`brew`来安装它。

`brew update && brew cask install java`

假设一切正常，你现在可以输入`lein repl`，这是一个试验 Clojure 的好方法，因为你可以输入代码并进行测试。

终端中的最后一行应该是类似于`user =>`的内容。如果你想退出 REPL，只需输入`exit`并点击回车键。

键入`(println "Hello, world")`并按回车键，您应该会看到

```
Hello, world
nil 
```

Enter fullscreen mode Exit fullscreen mode

### 零是怎么回事？

每次你执行一些 Clojure 比如`(println "hello world")`它*总会返回值*。它们被称为“表达式”。这是 Clojure 的优势，通过一致性增加了它的简单性。

想想你在其他编程语言中调用过的一些函数，有时候会返回值，有时候不会。

给定这个 Javascript

```
function myAdder(x, y) {
     x + y
} 
```

Enter fullscreen mode Exit fullscreen mode

哎呀没有结果！你忘了放`return`。

这种方法使得 Clojure 更加简洁，您不必担心显式返回，想象一下这会有多垃圾:

`(return + 20 (return - 50 20))`

在`println`的情况下，它不能返回任何有意义的东西，因为它只是打印到屏幕上，所以它返回`nil`，这意味着“什么都没有”。REPL 总是打印你运行的表达式的结果，所以在最后一种情况下你看到我们的`println`被打印，然后表达式的结果被打印(`nil`)。

如果你尝试`(+ 20 50)`，你会看到它只是打印`=> 70`。

### REPL 提示

*   在 REPL 内部，如果你不确定函数类型是什么，例如`(doc println)`
*   通常，您可以使用 tab 键来自动完成
*   按向上箭头键在前面的表达式中循环

### Clojure vs Javascript

以下是用 Clojure 和 Javascript 编写应用程序时会做的一些常见操作的例子。在 REPL 试用 Clojure 示例，并养成使用`doc`的习惯

请注意，与 Clojure 相比，在 Javascript 中执行这些任务的语法有所不同。

这并不是特别针对 Javascript，大多数主流编程语言都是如此。

#### 添加

Javascript: `1 + 1`

Clojure `(+ 1 1)`

#### 调用函数

Javascript `alert('Chris')`

Clojure `(println "Chris")`

#### 流量控制

java 描述语言

```
if(CHRISTMAS){
    return “PUT NUTMEG IN EVERYTHING”
} else {
    return “PUT SORROW IN EVERYTHING”`
} 
```

Enter fullscreen mode Exit fullscreen mode

Clojure

`(if :CHRISTMAS "PUT NUTMEG IN EVERYTHING" "PUT SORROW IN EVERYTHING")`

#### 用清单做事

Javascript
`["chris","ruth"].foreach(name => println('hello' + name)`

clo jure
T0】

## clo jure 有何不同？

你应该注意到所有这些东西的语法都是一样的

`(function argument1 argument2)`

你可以建立更复杂的表达式，就像你在数学中通过嵌套来做的一样。

`(+ 20 (- 30 50))`

内部表达式被求值，留给我们

`(+ 20 -20)`

也就是`0`。

在 JavaScript 和大多数其他主流语言中，参数和函数名可以以不同的顺序和位置出现，你还需要知道像`return`和`=>`这样的符号。难怪学编程这么难。

我发现 Clojure 最有趣的一点是语法完全统一。学习*如何*编写有效的 Clojure 的门槛非常低。编写真正有效的*clo jure*就像任何其他语言一样仍然是一个挑战，但是语法非常容易掌握。

这种方法的一个有趣的副作用是，你会逐渐理解`the code in clojure is data`。当你构建像`(+ 2 2)`这样的函数时，它实际上是一个数据的*列表*，包含函数和参数(就像一个名字列表`("chris" "ruth")`)。

有成就的 clojure-ists 用它来编写*代码，这些代码解析和操作被称为“宏”的代码*。现在不要太担心这个，但这是 Clojure 的一个非常有趣的特性。

## 秘密圣诞老人时间

现在你可能觉得自己是 Clojure 专家，所以让我们发布一些史诗代码。

### 值

当您编写代码时，通常需要将有用的信息存储在变量中以备后用。要在 Clojure 中做到这一点，没有神奇的特殊语法(正如我所承诺的！)就叫`def`。

在 REPL 尝试`(def my-santas ["Chris" "Ruth" "Turner" "Hooch"])`。

你在这里看到的唯一新语法是`[ ]`，它创建了一个名为`Vector`的数据结构，现在你可以把它想象成一个类似 Ruby 或 JS 中的数组。

如果你想得到你的变量，只需在 REPL 中输入`my-santas`。

### 你是如何秘密圣诞老人的？

我们需要某种算法来为神秘圣诞老人配对。当思考这些事情的时候，想想你在现实生活中如何解决这个问题通常是有帮助的。

*   我们需要一种方法来用数据表示配对的人；`x gives to y, y gives to z`
*   我们需要随机选择人群

我是如何做到这一点的，这只是扮演神秘圣诞老人的众多方式之一。我完全鼓励你尝试不同的方法！

当处理一个你不确定的问题时，最好是把问题分解成更小更简单的部分。

#### 代表我们的数据

首先，我们需要一种方式来表现我们的圣诞老人组合。`map`数据类型非常适合这种情况。如果你不熟悉的话，地图是一种类似数组的数据结构，但是它有一组带有相应值的键。

它们在 Ruby 中被称为 HashMaps，在 Javascript 中被称为 Objects。你可以广义地认为它们是字典。

我们可以在地图中表示我们的秘密圣诞老人的结果(地图的语法是`{key1 value1, key2 value2}`)

`{"chris" "ruth", "ruth" "hooch", "hooch" "turner", "turner" "chris"}`

即克里斯给露丝，露丝给霍奇，等等..

#### 创造我们的第一个表象

`zipmap`是 Clojure 中的一个函数，它接受两个向量并将它们“压缩”到一个地图中(非常有趣)。

所以在 REPL 试试`(zipmap my-santas my-santas)`

你应该得到`{"Chris" "Chris", "Ruth" "Ruth", "Turner" "Turner", "Hooch" "Hooch"}`

好的，每个人都只是给自己一份礼物，但我们的基本数据结构已经存在，我们可以看看是否可以调整第二个参数到`zip-map`，以这样一种方式，每个人都沿着一个移动，它可以工作！

试着用`zipmap`做实验，你认为`(zipmap my-santas [1 2])`会发生什么？

#### 旋转列表

我们不希望鲁思给鲁思送礼物，如果我们可以拿我们的第二份`my-santas`并改变它，那么每个人都会给不同的人

想象一下我们房间里的圣诞老人站成一排
`Chris Ruth Turner Hooch`

然后你克隆它们(就像我们的`zipmap`一样)

`Chris Ruth Turner Hooch`

`Chris Ruth Turner Hooch`

如果我们只是将克隆的行向右移动，并将末端的人放回起点…

`Chris Ruth Turner Hooch`

`Hooch Chris Ruth Turner`

每个人都可以把礼物送给他们面前的人，这样我们就有了秘密的圣诞老人。

我们如何做到这一点？

再次尝试简化问题

*   做一个新的向量
*   最后一项作为第一项
*   然后剩下的放在最后

Clojure 令人惊叹的标准库再次为我们提供了`last`来获取向量的最后一个元素，以及`butlast`来从最后一个项目中获取除之外的所有内容。随着函数`cons`让我们创建新的向量，让我们把它们粘在一起。

本着打破常规的精神，首先尝试`(cons (last my-santas) (butlast my-santas))`

然后把它放在一起

`(zipmap my-santas (cons (last my-santas) (butlast my-santas)))`

您可以使用换行符让代码稍微放松一下

```
(zipmap my-santas 
         (cons (last my-santas) (butlast my-santas))) 
```

Enter fullscreen mode Exit fullscreen mode

这导致了`{"Chris" "Hooch", "Ruth" "Chris", "Turner" "Ruth", "Hooch" "Turner"}`

厉害！克里斯给霍奇，露丝给克里斯，等等。

#### 随机化

这很好，但每次给我们的结果都是一样的。我们可以通过添加一些随机化来使我们的算法变得更好。

##### 洗牌

试着在 REPL 中键入`(shuffle my-santas)`，你会看到它需要一个`Vector`并洗牌。这对我们来说很有用，可以让我们的表达更令人兴奋。

##### 让

`let`是一个函数，允许您声明可以在传递给它的函数内部使用的值

`(let [x 1, y 2] (+ x y)`

*(记住这可能看起来有点奇怪，但它仍然遵循`(fn arg1 arg2)`的惯例。只是对于`let`它的第一个参数是一个向量，然后是一个要运行的表达式)*

在 Javascript 中是:

```
x = 1
y = 2
return x + y 
```

Enter fullscreen mode Exit fullscreen mode

实用程序是相同的，当编写重要的代码时，您需要捕获值并命名它们以提高可读性。回到我们的例子，我们想创建一个名为`random-santas`的变量，它存储我们洗牌后的圣诞老人，供其余代码使用。

##### 把一切粘在一起

让我们使用这些函数来使我们的算法更不可预测，并存储一些值来让我们的代码更容易阅读。

```
(let [random-santas (shuffle my-santas)
   shuffled-santas (cons (last random-santas) (butlast random-santas))]
   (zipmap random-santas shuffled-santas)) 
```

Enter fullscreen mode Exit fullscreen mode

代码现在读起来相对不错！

*   随机排列圣诞老人名单
*   创建一个名为`shufled-santas`的列表副本，其中每个人都移到了右边，我们将最后一个圣诞老人放在了最前面
*   将它们压缩在一起，创建我们的`Chris gives to Ruth`对

## 挑战自我

如果你想在不同的圣诞老人身上重复使用我们惊人的算法呢？

你可能想定义一个函数，这样你就可以像我们使用像`shuffle`和`zipmap`这样的函数一样使用它。

定义自己的函数的基本语法是

`(defn name-of-function [arg1, arg2, etc] function-body)`

### 举例

`(defn add-2 [number] (+ number 2))`

然后你可以像调用其他函数一样调用它`(add-2 10)`

试一试！写`secret-santa`。

## [T1】我们拯救了圣诞节！](#we-saved-christmas)

这可能很难理解，但我希望它至少是有趣的。也许你可以试着自己重新实现，看看你能走多远。你能想出不同的方法来写它吗？

可能更有趣的是用一种您更熟悉的语言来实现秘密圣诞老人。考虑一下您的实现与 Clojure 的实现有多么不同，特别是在您面对的所有不同种类的语法方面。

[这篇文章最初发表在我的博客上](http://www.quii.co.uk)