# TypeScript 简介-基础知识

> 原文：<https://dev.to/areknawo/typescript-introduction---the-basics-3pmd>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

如果你读过我以前的一些帖子或任何类型的 JS 博客，你可能听说过 **[TypeScript](http://typescriptlang.org/)** 。对于没有- **TS** (TypeScript 官方缩写)无非是**现代 JavaScript** 加上**静态类型系统**。而且，它最近获得了很多关注。这主要是因为它提供了优于标准的动态类型 JS 的优势。因此，在本文中，假设您已经了解 JavaScript，我将指导您迈出使用 TypeScript 的第一步。这将是一个**深度系列**，几乎涵盖从纯基础到一些复杂的一切。我会尽可能简单地解释这一切(专业人士——请不要指责我把事情过于简单化🙃).**尽情享受！**😉

[![forest trees marked with question marks](img/9453751ad10800dc491f4711ffa65b9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wm4bWCbp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1458419948946-19fb2cc296af%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 为什么(不是)？

在我们继续学习 TS 之前，让我们先来探索一下为什么**值得你付出**。

首先，TypeScript 是一种**编译语言**，它的默认编译目标是 JavaScript。这是一个由**微软**启动并监督的**开源**项目。它为现代 **ES-Next 特性**和**静态类型系统**提供内置支持。而且，虽然许多日常 JS 开发人员使用 Babel 等工具来利用 ES-Next，但静态类型的概念对您来说可能是新事物(除非您以前与其他静态类型语言交互过😁).

静态类型化使得变量的类型在编译时已知。JS 是一种带有动态类型系统的解释型或 JIT 编译型语言。但是，更重要的是，对你这样的编程语言终端用户(又名消费者)来说，重要的是它在现实世界中的用途。在这种情况下，静态类型带来了更好的易错性，并且通常更好地支持和**工具**。因此，它可以极大地改善你的**编码体验**。至于动力型系统，它也有它的优点。最主要的一点是不必直接在代码中指定类型。😅

除了提到的静态类型系统之外，没有更多的原因会产生任何大的不同。因此，如果您想学习 TS 并提高您的知识和开发经验，请跟随我并发现**静态类型**的潜在能力。

# 基础知识

TS 中提供的每个特性都是**非侵入性的**，这意味着它的语法不会与 JS 代码的任何部分重叠。这使得来回移植您的 JS 应用程序相对容易。

您可以使用冒号(`:`)后跟类型的实际名称:
来指定变量的类型

```
const myStringVariable: string = "str"; 
```

Enter fullscreen mode Exit fullscreen mode

有 6 种基本的**原语类型**需要记住:

*   **数字** -代表任何种类的**数值**-整数或浮点数、十六进制、十进制、二进制等。
*   **字符串** -代表任意一种**字符串**的值；
*   **布尔值** -代表任意一个**布尔值**，即`true`或`false`；
*   **符号** -代表`symbol`值；
*   **null** -仅代表`null`值；
*   **未定义** -仅代表`undefined`值；

这对你来说应该不是什么新鲜事。上述每一种类型都被恰当地记录为 JS 语言本身的一部分。因为 JavaScript 的动态类型系统，这个事实只被隐藏了一点点。但是，请放心，TypeScript 提供的远不止这些。我认为我们应该深入挖掘！🌟

# 更多类型！

## 物体

从更复杂的类型列表开始，我们有了`object`类型。需要记住的是，它代表任何非原始值。这意味着图元是不可赋值的。那是因为 JS 中几乎所有的东西都是对象。如您所见，TypeScript 非常尊重 JavaScript 架构。😀

```
const myObjectVariable: object = "str"; // error
const myObjectVariable2: object = {}; 
```

Enter fullscreen mode Exit fullscreen mode

## 任何

`Any` type，顾名思义，表示任何可能的值。它作为一种后备，允许你**省略类型检查**。从 JS 移植的时候一开始真的很有帮助。但是，不应该过度使用，甚至更好——根本不应该使用！你不会使用 TypeScript 只是到处键入`: any`吧？😂

```
let myAnyVariable: any = "str";
myAnyVariable = 10;
myAnyVariable = true; 
```

Enter fullscreen mode Exit fullscreen mode

## 作废

字体，顾名思义，代表完全没有字体。这通常与函数一起使用，告诉编译器函数不返回任何东西。在这种情况下，任何东西都包括`undefined`和`null`，但谁在乎呢？反正他们俩似乎*作废了*。😅供您参考，您很可能不会将这种类型与变量一起使用，但是看看它会有多奇怪吧:

```
let myVoidVariable: void = undefined;
myVoidVariable = null; 
```

Enter fullscreen mode Exit fullscreen mode

## 永不

`Never` type，按照纯粹的定义，代表**永远不会出现**的值。但是到底是什么意思呢？基本上，它指的是例如抛出/返回错误的函数的返回类型，这不允许函数有可到达的端点。它也和所谓的**型守卫**一起使用(后面会详细介绍)。一般来说，这种类型不常用。无论如何，后面会有例子，还有一些更高级的东西。

## 未知

未知是 TS 类型集合中相对较新的内容——它是在 *v3.0* 中引入的。这意味着它是对`any`类型的类型安全替代。像那样的东西是如何工作的？首先，任何值都可以赋给未知，就像`any` :

```
const myUnknownVariable: unknown = "str";
const myAnyVariable: any = "str"; 
```

Enter fullscreen mode Exit fullscreen mode

当把`unknown`类型的变量赋给其他变量时，差别就出现了。我的意思是:

```
let myUnknownVariable2: unknown = myUnknownVariable;
myUnknownVariable2 = myAnyVariable;

let myStringVariable: string = "str";
myStringVariable = myAnyVariable;
myStringVariable = myUnknownVariable; // error 
```

Enter fullscreen mode Exit fullscreen mode

Unknown 不可分配给除自身以外的任何事物...

这就是官方 TS 文档所说的，也是通常澄清任何和未知之间的区别。

[![black framed panto-style eyeglasses beside black ballpoint pen](img/105582938120f3d8470a33c4b85cc04f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gAfXR94d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1453906971074-ce568cccbc63%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 作文类型

到那时，我们已经发现了 **TS 原语**和**顶级类型**(这就是上一节中提到的内置类型的名称)。现在是时候探索一些更有趣的类型了。那些不总是有直接的 JS 对应的。我称它们为**作曲类型**，因为它们是由一些更小的部分组成的。澄清一下，这个名字无论如何都不是官方的。😁

## 工会

简而言之，**联合**允许你指定变量的类型，你可以给变量分配**不同类型的**值。它们的作用是列出可能的可赋值类型。它们可以通过写你的类型来指定，用管道符号(`|`)来划分。

```
let myUnionVariable: string | number = "str";
myUnionVariable = 10;
myUnionVariable = false; // error 
```

Enter fullscreen mode Exit fullscreen mode

工会类型有令人难以置信的潜力。你可以用它们来处理函数中不同类型的参数，或者用这些真正的**类型安全的**替代方法来替换你的`any`类型。

## 文字量

**文字**类型允许你严格定义变量的可能值。文字本身不是任何类型的复合类型，但是它们经常和联合等一起使用，所以我把它们放在这个类别中。那么，文字类型是什么样子的呢？嗯，就像它注释的一个可能的值:

```
let myStringLiteral: "str" = "str";
let myNumberLiteral: 10 = 10;
let myBooleanLiteral: true = true;

myStringLiteral = "string"; // error
myNumberLiteral = 1; // error
myBooleanLiteral = false // error 
```

Enter fullscreen mode Exit fullscreen mode

我认为通过上面的例子，你可以很容易地理解文字类型背后的思想，并且你可以想象这些与例如 unions 的集成有多好

```
let myVariable: "on" | "off" = "off";
myVariable = "on";
myVariable = "suspend" // error 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你想字面上(这是一个好词🙃)像对象一样表达一些更复杂的值？嗯，你做完全一样的事情:

```
let myObjectLiteral: {str: string} = {str: "str"};
myObjectLiteral.str = "string";
myObrjectLiteral.num = 10; // error 
```

Enter fullscreen mode Exit fullscreen mode

## 路口类型

**交集**类型与并集类型密切相关。联合类型的功能类似于逻辑 or，交集类型的功能类似于逻辑 and 和 T4。因此，您可以使用**和**符号(`&`)来创建它们。

```
const myIntersectionVariable: {str: string} & {num: number} = {
    str : "str",
    num: 10
}; 
```

Enter fullscreen mode Exit fullscreen mode

创建的类型具有所有操作数的属性。这些类型经常与对象文字和其他复杂的类型和技术一起使用，我们将在后面介绍。

## 数组

在所有这些类型之后，是时候见见优秀的老**阵列**了。在这里，我将向您介绍键入数组值的第一种方法。这是因为有两种方法可以达到同样的目标——稍后会详细介绍。现在，要表示数组类型，必须为数组的实际值编写类型，并用方括号符号(`[]`)继续。

```
const myStringArrayVariable: string[] = ["str", "str"]; 
```

Enter fullscreen mode Exit fullscreen mode

只是提醒你-你可以加入和一起使用许多以前见过的类型。例如，您可以为具有联合类型的字符串和数字数组创建一种类型，或者为文字值数组创建一种类型。选择是无穷无尽的！🤯

```
const myUnionArrayVariable: (string | number)[] = ["str", 10];
const myLiteralArrayVariable: ("str")[] = ["str","str"]; 
```

Enter fullscreen mode Exit fullscreen mode

我想到那时你已经知道在 TS 中额外的空间并不重要。另外，请注意上面代码片段中的圆括号(`()`)。就像在普通数学(还有 JS)中一样，他们习惯于**将东西组合在一起**。似乎很合理。😅

## 元组

与数组密切相关的结构，所谓的**元组**可以用来指定一个数组的类型，该数组具有**固定数量的元素**，它们都具有**严格指定的类型**。看一下例子:

```
const myTupleVariable: [number, string] = [10, "str"];
const myTupleVariable2: [string, number] = [10, "str"]; // error 
```

Enter fullscreen mode Exit fullscreen mode

这几乎解释了一切。要定义一个 tuple 类型，首先要用方括号(`[]`)来表示任何类型的数组，每个数组都包含一个 tuple 类型，用逗号分隔。再一次，非常理性的东西。

## 枚举

对于一些 JS 程序员来说，枚举可能有点陌生。但事实上，这些在静态编程语言社区中是众所周知的。它们用于简单地提供更多的**友好名称**到**数值**。例如，在配置对象等中要求不同的数字有一个共同的模式。这就是枚举找到它们的用例的地方。

枚举的定义方式与我们以前见过的任何类型都有所不同。即通过使用`enum`关键字。

```
enum Color {Red, Green, Blue}; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们定义了一个名为`Color`的枚举，有 3 个成员- `Red`、`Green`、`Blue`。默认情况下，这些成员从 0 开始编号，**每增加一个条目就增加 1** 。也就是说，通过使用 enums，您可以访问它的成员数值和名称！让我告诉你我的意思。😁

```
Color.Red // 0
Color.Blue // 2
Color[1] // "Green"
Color[2] // "Blue" 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，您可以很容易地将枚举用于标准值。但是我们在 TS 中，我们在这里都是关于类型的，那么如何使用枚举作为类型呢？嗯，很简单——就像其他类型一样！

```
let myColorEnumVariable: Color = Color.Red;
myColorEnumVariable = 2;
myColorEnumVariable = Color[1]; // error 
```

Enter fullscreen mode Exit fullscreen mode

所以，我认为一个枚举类型的变量实际上可以作为一个数字文字的并集。您可以为其分配适当数量或值的枚举成员。不允许其他值，即使是成员的名字。

现在，关于枚举成员编号的一个快速注释。正如我所说的，默认情况下，它从 0 开始，每增加一个成员就加 1。但是你可以通过直接赋值来改变它。

```
enum Color {Red, Green = 32, Blue};
Color.Red // 0
Color.Green // 32
Color.Blue // 33 
```

Enter fullscreen mode Exit fullscreen mode

而且，在上面的例子中，我们已经覆盖了`Green`成员的值。这样，`Red`的值保持默认的- 0 不变，`Green`被赋值为 32，而`Blue`由于增加 1 的规则而为 33。

总之，如果使用得当，枚举是非常有用的，而且我认为，对于 JS 用户来说，它们是最难记住的或者说是最新的语法之一。但是当我们谈到接口时，这将是非常有用的，所以让我们继续！⚡

# 功能

学完了上面所有的类型和东西，我想是时候终于知道如何正确的**类型函数**了！有了这些知识，您应该能够真正开始编写一些 TS 代码了！

键入函数类似于我们以前编写的其他 TS 代码。我们仍然有冒号和常见的类型名语法，但是在不同的地方。

```
function myFunction(myStringArg: string, myNumberArg: number): void
{
    // code
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，函数的 arguments 部分后面是我们的标准类型注释。它通知编译器关于函数的**返回值类型**。在上面的例子中是`void`。我在前面谈到这种特殊类型时提到过，它实际上表明根本没有任何类型。这意味着我们上面的函数不返回任何东西。简单吧？

当然，键入一个函数不仅仅是上面的代码片段所能展示的。如果我们想要键入一个函数表达式，这是最近非常常见的，由于**箭头函数**的流行。那么，怎么做呢？

```
const myFunctionExpression: (arg: string, arg2: number) => void =
(arg, arg2) => {
    // code
} 
```

Enter fullscreen mode Exit fullscreen mode

上面你可以看到**函数类型**的样子。它和标准的箭头函数有相似的外观，不是吗？

```
(arg: string, arg2: number) => void 
```

Enter fullscreen mode Exit fullscreen mode

我们为变量提供了一个函数表达式，其中我们的参数没有类型化。那是因为我们已经在函数类型上做了，我们不需要重复。

函数类型就像任何其他类型一样，也可以用作另一个函数的参数类型。

```
function myFunction(funcArg: () => void): void {
    // code
} 
```

Enter fullscreen mode Exit fullscreen mode

这里作为一个参数，我取一个函数，它不接受任何参数，也不返回任何东西。同样，记住这些可以很容易地与其他 TS 类型混合。😉

但是如果你想要一个额外的，不需要的参数呢？如何注意到某个东西只是**可选**？很简单——在你的论点名称前面加上**问号** ( `?`)！

```
function myFunction(myArg: number, myOptionalArg?: string): void {
    // code
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以有不止一个可选参数。但是，由于显而易见的原因，它们后面不能跟任何类型的必需参数。定义可选参数有一个更长的语法，你已经想到了吗？

```
function myFunction(myArg: number, myOptionalArg: string | undefined): void {
    // code
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，这个问号只是把你的类型和`undefined`联系起来。而且，由于可选事物的相同语法在更多的地方被使用，值得知道的是它不能在任何地方被使用。在这样的地方，你可以使用上面的语法，它总是有效的。😁

[![grayscale photography of vintage car engine](img/086c057fdf321dec73a77f6d7233f524.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qf0EHkqq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1458942521101-2f2fb506cee3%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 更多一些功能

至此(如果您自然地阅读了这篇文章)，您已经对一些 TS 类型有了很好的理解——一些基本的和更复杂的类型。但是 TS 的意义远不止于此！所以，让我们探索一些有趣的东西，让你的 TS 生活**更轻松**！👍

## 式推断

到目前为止，在所有以前的片段中，我们严格地逐个定义我们的类型。似乎静态类型语言需要更多的编写工作！别害怕，我的朋友-他们不会的！这些语言中的许多都有所谓的**类型推断**，它允许编译器在没有任何特殊注释的情况下为特定变量选择合适的类型。因此，在声明变量时就给变量赋值的地方，或者当涉及到函数的返回类型时，您可以随意删除类型注释，并仍然利用所有静态类型的优点。

```
const myStringVariable = "str"; // string
const myNumberVariable = 10; // number
const myObjectVariable = {
    str: "str",
    num: 10
}; // {str: string, num: number} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，类型推断使我们的代码看起来更干净，整体上更好。

类型推断的工作原理是推断出最常见的类型。这意味着推断出的类型应该尽可能的通用。因此，如果你想只允许严格定义的文字类型，你仍然必须严格地注释它们。

## 型卫士

记得工会吗？当我不久前介绍他们的时候，你想过工会是如何处理某些事情的吗？因为，你知道，当一个变量具有类型`string`时，IDE 可以使用该信息并为你提供许多有用的东西，例如，JS `string`类型方法的正确的**自动完成**。但是，当变量的类型为`string | number`时，IDE 只能显示这些类型之间共享的方法。更糟糕的是，你只能把这样的变量赋给直接指定允许`string | number`的地方。但是如果你想像这样分别给类型`string`或`number`赋值呢？

好吧，考虑一下。首先，您必须确保可以有两种类型的变量完全是所需的类型。你如何实现这一点？拥有所谓的**型近卫**。而 type guards 只不过是一个花哨的 TS 名，对于你们这些 JS 操作者来说可能很熟悉:`typeof`和`instanceof`。在 TS 中，它们没有比 JS 更多的功能，所以您可以像在 JS 中一样使用它们。他们在幕后所做的，是将你的变量的类型**限制为某种类型**——在我们的例子中是`number`。

```
const myUnionVariable: string | number = 10;

function myFunction(arg: number) {
    // code
}

myFunction(myUnionVariable); // error
if( typeof myUnionVariable === "string" ){
    myFunction(myUnionVariable);
} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以通过检查值是否有某个属性，是否等于某个值等等来轻松定义自己的类型保护符。这种类型的保护采取某种返回类型的函数形式。

```
function isOne(num: number): num is 1 {
    return num === 1;
}

const myNumberVariable: number = 1; // number
isOne(myNumberVariable) // 1 
```

Enter fullscreen mode Exit fullscreen mode

您的类型保护是一个返回布尔值的函数。如果这是真的，你的参数就采用之前注释过的类型。这个注释是在带有关键字`is`的函数返回类型中完成的，左边是参数的名称，右边是函数返回`true`时要转换的类型。相当简单明了，但是对于复杂的结构和类型非常有用。

## 铸造类型

类型转换(也称为类型断言)是许多(如果不是全部)静态类型语言中使用的一种非常强大的技术。当你比编译器更了解变量的类型时，这很有用。当你的编译目标是一种动态类型的语言，比如 JS 时，这种情况尤其常见。基本上，它允许你通过**强力**改变变量的类型，没有任何限制。👊在 TypeScript 中，有两种不同的语法用于此目的。

```
const myAnyVariable: any = "str";

const myStringVariable: string = <string>myAnyVariable;
const myStringVariable2: string = myAnyVariable as string; 
```

Enter fullscreen mode Exit fullscreen mode

您可以在强制转换的变量前加上尖括号(`<>`)和目标类型，或者使用`as`关键字后跟目标类型。这些方法没有区别，所以请随意选择您的最佳方法。

现在，在上面的例子中，我将`any`类型的变量转换为`string`，但是如果你不在代码中使用`any`(**强烈推荐**)，你甚至可能不会偶然发现这一点。但是，信不信由你，类型转换有更多的使用案例。只是要确保**不要过度使用它**，因为它会在不经意间极大地限制代码的类型安全。

# 大量覆盖

如果到目前为止你已经阅读了这篇文章，那么恭喜你——你已经迈出了打字稿和静态类型语言世界的第一步。但是还有更多的内容要介绍！所以，如果你喜欢这篇文章，考虑与其他非常想学习 TS 的人分享**[**it**](https://www.facebook.com/sharer/sharer.php?u=https://areknawo.com/typescript-introduction/)，并在下面留下拇指或任何反应，让我知道**如果你想要后续的**，我将在那里讨论更高级的主题，如**接口**、**泛型**以及其他一些很酷的**技术**！另外， **[在 Twitter](https://twitter.com/areknawo)** 上关注我，在我的脸书页面 上 **[关注我，了解这个系列的最新消息以及更多关于 JS 的有趣内容！](https://www.facebook.com/Areknawos-Blog-770620059945105)****

 **我希望这篇文章能带给你一些关于 ts 的见解，并鼓励你拓宽知识面。再次抱歉，如果我没有解释一切超级详细和技术，因为这是一个友好的，介绍性的教程。😁无论如何，感谢阅读，下次再见！🚀**