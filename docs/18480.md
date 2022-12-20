# 你好世界！瞄准

> 原文：<https://dev.to/areknawo/hello-world-in-aim-2c7>

这是 AIM 项目系列的延续，所以如果你还没有阅读 T2 的介绍文章，那么花点时间去读一读。

所以，让我们从语法概念开始这个项目。这是语言设计中最容易也是最难的部分。正如我在上一篇文章中所说，AIM 的语法与任何其他编程语言相比都有所不同，事实也的确如此。你可能喜欢或不喜欢，但它来了。🎉

[![person holding light bulb](img/517b8de88219f9538fcc9fa245641e16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i7daNtX0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1493612276216-ee3925520721%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 照片由[迭戈 PH ](https://unsplash.com/@jdiegoph?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

# 想法

当把这种语法和 AIM 语言作为一个整体来考虑时，我主要是被我在 web 开发中的个人经验所驱使。我希望这种语言面向开发本地、现代和交互式应用程序的人。还要牢记语言结构的**性能**、**反应性**、**易用性**和**逻辑顺序**。AIM 的开发应该是简单的，不应该有太大的学习曲线。另外，我从实现的角度考虑了语法。它的 **AST** 应该是**不复杂的**，**简单的**以备后用，方便编辑器自动完成，美化工具，过梁等。现在，不局限于遵循大多数语言的语法形式标准，我想我创造了一些新的东西，但确实与正在使用的东西相似。

# 语法

我有幸了解到设计一门好的(或任何相关的)编程语言有多难。当然，你可以很容易地发明一些新的语言结构等等。但是把它们变成一个逻辑序列，那就是另一回事了。同样，你必须记住你发明的特性必须是可以实现的(特别是如果你自己要做的话)。

为了设置，我在 GitHub 上为这个特定的项目创建了一个 [monorepo](https://github.com/areknawo/AIM) 。到目前为止，我已经开始为这种语言的语法编写[文档。对于接下来的开发，有一个集中的基础/参考点是非常重要的。否则，你会很快迷失在这一切之中——相信我。👍因此，这份文件是高度**的工作进展**，但至少应该是可读和理解的大多数。😂当然，随着时间的推移会越来越好。作为现在的开源，任何**拉请求**、**想法、**和**建议**都**真的真的欢迎**！为了阅读这篇文章的以下部分，建议阅读提到的文档，或者至少看一看。](https://github.com/areknawo/AIM/tree/master/syntax)

# 打招呼！

让我们从简单和标准的“ ***Hello World”的基本语法概述(在其当前状态下)开始。** *。从一开始，我就为这种新的编程语言思想的不太好的语法强调感到抱歉。🙃

```
>> Say hello!
{
    console = @import('console')
    <>{
        hello #string = "Hello World!"
        ..console.log(hello)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来讨论我这个漂亮的怪物一样的创作！

首先是一个评论。**单行**注释在 AIM 中以 **> >** 符号开始。对于**多行**注释，我认为其开头与单行注释相同，后面用 **< <** 符号结尾。这种解决方案简化了语法，但可能会给实现带来一些麻烦。可能需要为多行注释设置行数上限或更改其语法。这是因为您必须知道这个评论是否只是一行程序。如果你有任何想法如何解决这个问题，那么我将不胜感激。

回到代码，我们终于开始写目标代码了。我们从基本代码所在的**主代码块**开始。我马上会回到这个话题，但现在让我们来探讨 AIM 中的**代码块**和**高级类型**的概念，因为这对整个语法非常重要。

所以，在 AIM 中没有**没有**这样的东西，比如**函数**、**对象**、**接口**或者**类**(至少在字面上是这样的)。所有这些可以在其他面向对象语言中找到的结构在 AIM 中都被所谓的**高级类型**所取代。这些允许你用一个简单通用的语法来表达前面提到的所有结构。你应该知道**代码块**、**接收器**和**滑槽**的三种高级类型。

### 代码块

Codeblock 是所有高级类型中最重要的。它只由**花括号**和其中的代码组成。代码块只是允许您对代码进行分组。当然，它们可以嵌套、赋给变量或作为参数传递——就像普通类型一样。

```
myCodeblock #codeblock = {} 
```

Enter fullscreen mode Exit fullscreen mode

### 接收器

接收器是代码块的**助手类型**的一种形式。这些允许将参数传递到代码块的本地范围。因此，通过将接收器与 codeblock 结合起来，您可以创建通常被称为带参数支持的函数的结构。

```
myReceiver #receiver = <arg1 #int32, arg2 #int32> 
```

Enter fullscreen mode Exit fullscreen mode

### 跑步者

运行者允许**执行写在代码块中的**代码。当 codeblock 与接收器绑定时，您可以使用 runner 来传递给定参数的值。

```
myRunner #runner = (1,2) 
```

Enter fullscreen mode Exit fullscreen mode

### 都在一起

如您所见，高级类型只是类型，您可以为它们中的每一个创建单独的变量。然后使用**绑定修改器**使它们相互作用。

```
>> take arguments and run codeblock
myReceiver => myCodeblock => myRunner 
```

Enter fullscreen mode Exit fullscreen mode

你也可以一起定义它们，那么这样构造的类型将是**代码块**，因为**接收者**和**跑者**只是**辅助类型**。使用静态值时，可以省略 Bind 修饰符。

```
myBindedStruct #codeblock = <a #int32>{}(1) 
```

Enter fullscreen mode Exit fullscreen mode

## 回码

```
>> Say hello!
{
    console = @import('console')
    <>{
        hello #string = "Hello World!"
        ..console.log(hello)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，在我们的代码开始执行的**主代码块**中，我们看到了第一个变量。它的名字是*控制台*，它是**用**赋值修饰符**赋给 **@import 指令**执行的结果**。指令只不过是前面的 **@** - **指令修饰符**所指的类似代码块的结构。这些提供了扩展语言功能的主要方式。在这个例子中，我们使用 **@import** 指令从 **stdlib** 中导入一个模块，称为**控制台**来与控制台/终端交互。请记住，**标准库**还没有被定义，所以这个**控制台**模块只是为了这个例子的目的(尽管它真的有可能在未来出现)。

接下来，我们有一个与接收器绑定的代码块。它没有被赋给任何变量，所以它被称为代码块的主方法**。这只是一个你可以放置你想要执行的代码的地方(当你想要分离你代码的这个主要部分的时候)。当使用代码块作为类时，这是最常用的(这些代码块将作为**构造函数**)。它**必须**与一个**接收器**绑定，而**不应该被**赋给任何变量。当编写代码的开始部分时(像 ***Hello World！你必须使用它(与 C/C++中的 main 方法进行比较)。****

在这个**主方法**中，我们使用**类型修饰符**和**类型标识符**(字符串)定义了一个字符串类型的变量。然后，在下一行，我们使用**上下文修饰符** ( **..**)到**访问上层作用域**并从父代码块的上下文中访问**控制台**变量。接下来，我们将它与 runner 一起使用，并将我们的变量作为参数传递。这应该输出***“Hello World！”** *到控制台。

[![question mark neon signage](img/34e95238bde3479ffb94e0257ec34497.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JQiLRYew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1484069560501-87d72b0c3669%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 照片由[艾米丽·莫特](https://unsplash.com/@emilymorter?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

# 你有什么看法？

我知道上面写的一些东西可能不像它们应该的那样清楚，但是，正如我所说的——你总是可以阅读[文档](https://github.com/areknawo/AIM/tree/master/syntax)。此外，如果您对这种语法感兴趣，并且想帮助它变得更好——考虑一下在 GitHub 上的 pull-request，或者给我一些建议。当然，在这之前，先看看文件。😂如果你喜欢这篇文章或者这个系列背后的一般思想(编程语言开发)或者 AIM 本身——考虑在 [Twitter](https://twitter.com/areknawo) 上关注我的更多更新。最后但同样重要的是，如果你想更直接地跟随 AIM 发展，可以考虑[离开 a 星](https://github.com/areknawo/AIM)。🦄