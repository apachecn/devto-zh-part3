# 小心 Java 中的扫描器方法

> 原文：<https://dev.to/renegadecoder94/be-careful-with-scanner-methods-in-java-460m>

对于新学生来说，用 Java 编写使用 Scanner 的交互式程序通常很有趣。不幸的是，对于这些学生来说，有一些令人讨厌的陷阱并不能真正带来积极的体验。结果，我带来了一个警告:小心 Java 中的 Scanner 方法。

## 学生遇到的问题

我第一次学 Java 的时候，一次都没用过 Scanner。事实上，在我已经有了两年的行业经验之前，我甚至没有接触过这个工具。但是不管出于什么原因，我目前所在机构的 Java 课程广泛使用 Scanner，所以我想我应该谈谈 Scanner 的一些方法的缺陷。

特别是，我想谈谈如何使用 Scanner 从命令行读取用户输入。通常，这是可取的，因为我们希望提示用户输入一些信息，比如他们的名字或喜欢的号码。然后，我们做一些有趣的计算，并将结果输出给用户。我举个例子(用户输入加粗):

> 输入你的名字:**杰里米**
> 你好，杰里米！

在这个例子中，我们已经提示用户输入他们的名字，并且我们已经返回给他们。我们可以使用下面的 Java 代码片段来做到这一点:

```
Scanner input = new Scanner(System.in);
System.out.print("Enter your name: ");
String name = input.nextLine();
System.out.println("Hi, " + name + "!"); 
```

Enter fullscreen mode Exit fullscreen mode

那还不算太糟！但是，如果我们想要一个数字呢？对我们来说幸运的是，Scanner API 有一大堆像`nextLine()`这样的标记化方法，可以从用户那里获取我们想要的任何东西，并自动将其转换成适当的类型。也就是说，使用它们时要小心。

## 学生渴望的解释

此时，很多学生会问我:

> 有什么大不了的？当我可以直接调用适当的方法时，为什么我必须调用`nextLine()`并手动解析值？
> 
> 各地的学生

而且，他们的担心是完全有道理的！我从来不会要求任何人做不必要的工作，所以这有什么大不了的？

### 问题

好吧，事实证明，如果你开始使用像`nextLine()`这样的方法，而没有真正注意你正在解析什么，你会遇到一些问题。

也许最明显的问题是打字问题。例如，假设我们向用户要一个号码，他们给我们一个名字。如果没有适当的错误处理，我们的解决方案肯定会崩溃。也就是说，这种问题并不难发现或解决。毕竟，一旦用户输入无效文本，解决方案就会崩溃，并显示一个有用的错误。

相反，有一个更严重的问题，即使是经验丰富的程序员也会认为很难解决，当我们开始混合使用各种扫描方法时，这个问题就会浮出水面。看看下面的例子，看看如果我们给程序提供数字 25 和我的名字，你能不能算出会发生什么。

```
Scanner input = new Scanner(System.in);
System.out.print("Enter your age: ");
int age = input.nextInt();
System.out.print("Enter your name: ");
String name = input.nextLine();
System.out.println("Your name is " + name + " and you are " + age); 
```

Enter fullscreen mode Exit fullscreen mode

如果不深入研究这个解决方案，一般人会说这个解决方案会打印“你的名字是杰里米，你 25 岁”。老实说，我一点也不责怪他们。逻辑是有的，但结果却没有。幸运的是，我不会把你晾在一边！

### 猎虫

如果您已经跳过这一步，亲自测试了这个解决方案，您会注意到一些奇怪的事情发生了。首先，我们的产出是错误的。输出将不是“你的名字是杰里米，你 25 岁”，而是“你的名字是，你 25 岁”。马上，我们会注意到输出中缺少名称，这意味着[名称变量](https://therenegadecoder.com/code/primitive-types-and-variables-in-java/)可能存储了一个空字符串。

更糟糕的是，当我们实际运行该解决方案时，我们将会看到只提示我们输入一次。在我们输入年龄后，整个脚本将转储到屏幕上，如下所示(用户输入以粗体显示):

> 输入你的年龄: **25**
> 输入你的名字:
> 你的名字是，你 25 岁

在这一点上，我们会感到困惑不解，为什么对`nextLine()`的调用会立即返回一个空字符串。还好我有答案！

### 转义序列

每当我们提示用户输入时，他们会得到这个有趣的行，在那里他们可以转储一些文本并按下 enter 键。请注意最后一部分，因为它至关重要。

该文本由字符组成，这些字符都有一个潜在的数值。例如，字母“a”的数值是 97，而数字“7”的数值是 55——说来也奇怪。幸运的是，没有必要记住所有这些值。相反，[检查一个 ASCII 表](http://www.asciitable.com/)以获得前 256 个字符的列表。

如果我们浏览该表，我们会注意到有一些字符有点奇怪。例如，该表的整个第一列包含一个转义序列列表。这些角色有一个特殊的目的，在屏幕上并不总是清晰可见。

事实证明，有一些转义序列负责表示文本中的行尾:10(换行)和 13(回车)。根据系统的不同，这两个转义序列的任意组合都可以用来标记一行的结束。

### 流氓新台词

为了便于讨论，我们假设我们的系统用换行符标记新行。在 Java 中，换行符可以用' \n '来写。换句话说，每当我们按下回车键时，我们可以想象这些字符中的一个偷偷溜进了我们的文本。

既然我们知道了转义序列，为什么我们不试着再找一次 bug 呢？请注意我们声明年龄变量的那一行。这就是漏洞开始显现的地方。

```
Scanner input = new Scanner(System.in);
System.out.print("Enter your age: ");
int age = input.nextInt();
System.out.print("Enter your name: ");
String name = input.nextLine();
System.out.println("Your name is " + name + " and you are " + age); 
```

Enter fullscreen mode Exit fullscreen mode

你发现窃听器可能在哪里了吗？我给你一个提示。`nextInt()`方法只关心抓取下一个数字。换句话说，它留下了我们的流氓换行符(' \n ')。

在我们调用`nextInt()`之后，我们提示用户输入他们的名字。在这一点上，我们调用`nextLine()`直到下一个新的行字符。不幸的是，当我们呼叫`nextInt()`时，我们留下了一个。因此，`nextLine()`不必等到我们输入任何文本。它只是返回一个空字符串。

如果这还不清楚，我建议尝试将该代码片段放在由空格分隔的年龄列表上方。例如，查看以下脚本(用户输入为粗体):

> 输入你的年龄: **20 19 31 15**
> 输入你的名字:
> 你的名字是 19 31 15，你 20 岁

正如我们所看到的，年龄被设置为列表中的第一个数字(在本例中为“20”)，程序从不提示输入姓名。相反，输入缓冲区的剩余部分(在本例中为“19 31 15\n ”)作为一行读入，并保存为名称。

在下一节中，我们将讨论如何处理留下的换行符。

### 补丁作业

幸运的是，这个问题有几个修复方法。一个依赖于发现我们的错误，另一个依赖于解决问题的根源。

既然我们知道我们已经留下了一条新的线，我们所要做的就是消耗它。换句话说，我们可以先空调用`nextLine()`，然后再像平常一样调用:

```
Scanner input = new Scanner(System.in);
System.out.print("Enter your age: ");
int age = input.nextInt();
System.out.print("Enter your name: ");
input.nextLine();
String name = input.nextLine();
System.out.println("Your name is " + name + " and you are " + age); 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们是如何在存储名称的位置上方添加对`nextLine()`的调用的。现在，当我们离开这个' \n '字符后，我们将清理第 5 行中的混乱。然后，我们可以在 6 号线继续我们的生活。

或者，我们可以通过用对`nextLine()`的调用替换我们的`nextInt()`调用来完全消除这个问题。然后，我们需要解析我们的字符串来得到我们想要的整数:

```
Scanner input = new Scanner(System.in);
System.out.print("Enter your age: ");
int age = Integer.parseInt(input.nextLine());
System.out.print("Enter your name: ");
String name = input.nextLine();
System.out.println("Your name is " + name + " and you are " + age); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以一次性使用整行代码并手工解析，而不是事后清理。就个人而言，我更喜欢这个选项，但两者都很好。在这两种情况下，我们已经完全消除了这个讨厌的扫描器错误。

## 开放论坛

所以，这个故事的寓意是要小心扫描仪 API。虽然有许多方法是为了方便您，但是混合使用它们会导致一些难以跟踪的令人讨厌的错误。当然，你自己决定用这些新知识做什么。

无论如何，现在就这样吧！如果你喜欢这篇文章或者你有一些反馈，请在评论中告诉我。另外，如果你有任何自己的在 Java 中处理 Scanner 的技巧或诀窍，请不要犹豫，来分享。

如果你感觉特别慷慨，[订阅我的网站](https://therenegadecoder.com/members/)，叛逆的程序员。