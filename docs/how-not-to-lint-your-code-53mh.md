# 如何不对代码进行 lint？

> 原文：<https://dev.to/areknawo/how-not-to-lint-your-code-53mh>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

作为一个程序员，我觉得你对自己写的代码期望很高。在不久的将来，你要与之互动的那些人应该很容易阅读和理解它。这包括作者本人，例如 1 年后。当你看一下你的旧代码，不知道给定的片段是做什么的，那么它显然写得不好。这就是为什么每个程序员都有一个目标，那就是为他们编写的每个 LOC 提供完美的、**可伸缩的**架构和严格的**代码风格**定义。虽然项目的架构和结构是极其重要的方面，但是没有一种单一的方法可以让它适合所有人。这就是为什么在这里，在这篇文章中我不会谈论这些事情。让我们来谈谈代码风格，或者更具体地谈谈**代码林挺**。

**林挺**是分析你的代码潜在编程**错误**、**错误**、样式错误等的过程。但是，我猜你已经知道了(也许除了没人关心的严格定义😉).在现代代码中，编辑器和 IDEs linters 提供了在**实时检查**的帮助下编写更好代码的能力。在 JS 开发领域，有一些非常有用的工具，包括 [**ESLint**](https://eslint.org/) 、[、JSLint](https://www.jslint.com/) 和 [**JSHint**](https://jshint.com/) 。有了深度定制和扩展选项，它们肯定提供了足够的空间来为任何给定的项目创建与您的代码风格相匹配的最佳配置。也就是说，创建这些配置可能不是一件容易的事情，尤其是当您不知道特定的规则是否重要的时候。🤔

# 这是怎么回事？

很高兴你问了。🙃我想给你讲一个我的故事。一个关于**配置**我的棉绒以及它是如何结束的故事。公平地说，这一切的重点并不是说棉绒不好或复杂。相反，我只是分享我的一个有趣的小故事。

首先，你可能知道，如果你读过我以前的帖子，我是 [**打字稿**](http://typescriptlang.org/) 的忠实粉丝。我在几乎所有的未来 JS 项目中使用它。这一次也不例外。因此，我换入 TypeScript 并设置我的工作目录。由于这个项目是公开的，我决定**代码风格**是这里的一个重要因素。所以我用了 [**更漂亮的**](https://prettier.io/) 。如果你还不知道，这是一个* *格式化**/美化你的代码的工具，没有林挺，只是格式化。因此，它对字符串、线的长度等细节很好。但是，当然，这不是我们的终点。

然后，我把注意力转向了 TypeScript 配置文件- **tsconfig.json** 。我希望有尽可能严格的规则集，所以我打开了一些重要的选项。

*   确保你的函数在所有可能的情况下都返回(值或者什么都不返回)

*   严格的——这个有点棘手。它将另外 5 个选项整合到一个简单的包中:

    *   **无插入** -确保你的代码中没有任何类型
    *   **noImplicitThis** -不允许引用任何类型的 This
    *   **alwaysStrict** -在严格模式下解析您的代码，并使用“使用严格”标志
    *   确保你永远不会访问一个空值的属性
*   **noUnusedLocals** -检查未使用的局部变量

这是一个非常好的选择。这样，TypeScript 本身保证了我整体上更好的代码质量。但这对我来说还不够。😅

所以，接下来，我安装了 [**TSLint**](https://palantir.github.io/tslint/) ，这基本上是一个用于 TypeScript 的 linter(具有额外的基于类型的规则)。这就是事情开始变得有趣的地方...

# [功能区设置](#tslint-setup)

TSLint 是一个相当不错的工具。它有大量的**内置**林挺规则(带有创建所有者的选项)、一些默认配置(也可以扩展)等等...除此之外，它对 [**语言服务协议**](https://github.com/Microsoft/language-server-protocol) 的支持最近也得到了改进。这基本上意味着更好更快的 IDE/代码编辑器支持更复杂的规则，这很好。

于是，我开始从**下载[**ts lint-config-Airbnb**](https://github.com/progre/tslint-config-airbnb/)。这是遵循 [**Airbnb 的 JS 风格指南**](https://github.com/airbnb/javascript) 的一个非官方(**不是 Airbnb** 做的)TSLint 配置。它很好地为你提供了最好的 JS 林挺规则。它扩展了 I . a .[**tslint-eslint-rules**](https://github.com/buzinas/tslint-eslint-rules)(为 TS lint 提供 ESLint 中存在的规则)和[**TS lint-Microsoft-contrib**](https://github.com/Microsoft/tslint-microsoft-contrib)，后者增加了更多规则，直接来自微软(TS 最初的来源)。除此之外，我不得不使用[**tslint-config-appellister**](https://github.com/prettier/tslint-config-prettier)来禁用所有可能与我一直在使用的 appellister 冲突的规则。综上所述，你会得到一个非常有价值的设置。事实也的确如此。简单、直接，无需更多配置。**

 **但是与 [**tslint:all**](https://palantir.github.io/tslint/usage/configuration/) 相比，所有这些规则都显得微不足道。这是内置配置打开**所有**合理 [**内置规则**](https://palantir.github.io/tslint/rules/) 这就是我打开的！🙃让我们来谈谈它是如何结束的吧！

# 一个配置统治一切

事实上，我已经写了一些 TS 代码，很容易立刻感受到变化。而且，是的，有很多红色(意味着突出显示的错误)，很多！但是，这对我来说并不新鲜(之前对 linter 的配置做了很大的改动),我满怀热情地开始工作。相信我，知道现在你的代码会更清晰、可读(如果你配置正确的话)并遵循某种**标准**，修复/更改你的代码会很有趣。

开始的时候还不错。这种严格的配置确保了我没有未检查的未定义值和任何其他遗留值。没有任何类型的选择😅才能存在。 [**适当的文档**](https://palantir.github.io/tslint/rules/completed-docs/) 对于每个还没有的方法或属性或函数都是必需的。 [**更严格 if 检查**](https://palantir.github.io/tslint/rules/strict-boolean-expressions/) 、 [**字母排序**](https://palantir.github.io/tslint/rules/object-literal-sort-keys/) 和 [**类成员排序**](https://palantir.github.io/tslint/rules/member-ordering/) 基于访问修饰符显然为代码提供了一些额外的结构。这让我确信，我和任何未来的贡献者都将有明确的指导方针可以遵循。但事实是，如果打开所有规则的配置如此之好，难道每个人都不会只使用这一个，或者至少不会成为开箱即用的选项吗？所以，在这些好的、合理的规则被实现之后，是那些令人讨厌的规则出现的时候了...

[![brown ram](img/7473ec034ebffbbd42400f497dcf7ec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DfPSNsCB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1517128076055-be58e94ba823%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 冲突计数器

也就是说，让我们来看看仅仅因为 all-rules-on 配置而出现的最显著的问题和冲突。

## 数组类型

在 TypeScript 中，可以用两种方式编写数组类型——作为数组文字(`string[]`)或者作为通用数组类型(`Array<>`)。那么，有什么问题呢？嗯，这里来自于[**ts lint-Microsoft-config**](https://github.com/Microsoft/tslint-microsoft-contrib)附加规则叫做 **prefer-array-literal** 。与内置的 [**数组类型**](https://palantir.github.io/tslint/rules/array-type/) 规则冲突。第一种，顾名思义，推荐你使用**字面语法**。第二个基于数组项的传递类型的复杂性推荐语法**。肯定有冲突。我通过关闭数组类型规则解决了这个问题，这样就留下了我更喜欢的数组字面语法。**

## 神奇数字

嗯，这可能不是一个真正的问题，但可能会很麻烦。你听说过 [**神奇数字**](https://en.wikipedia.org/wiki/Magic_number_(programming)) 吗？这是一个术语，用来指代码中出现的没有任何意义的不同数字(是的，那是一个**反模式**)。考虑下面的例子:

```
for(let i = 0; i < 10; i++) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，数字 10 是**神奇的**，因为它不知从哪里出现，并且不是每个人都知道它真正做什么(但是很明显它只是使循环迭代 10 次，对吗？).所以，让我们应用一个快速修复。

```
const numOfIterations = 10;
for(let i = 0; i < numOfIterations; i++){
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我知道你可以在变量命名方面做得更好，不像我。😅但是，基本上，这就是幻数背后的全部魔力。🙃现在，要明确的是，这不是一个问题-这是一个非常好的做法，以命名您的数字，使每个人都知道他们都是什么。但是在上面这种情况下，这看起来有点不太直观，但是一般来说这是非常有用的。

# 未定义

接下来，我对未定义的值有一些问题。首先是 [**严格布尔表达式**](https://palantir.github.io/tslint/rules/strict-boolean-expressions) 规则。它的作用是强迫你在需要的地方使用真正的布尔值。这是什么意思？看看这样的东西:

```
if(possiblyUndefinedVariable){
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这是检查变量是否未定义的方法，可能许多 JS/TS 开发人员都使用这种方法。但是这条规则强迫你用更严格的方式来写，像这样:

```
if(possiblyUndefinedVariable !== undefined){
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，是的，有点长，但做同样的事情更明确的语法。

让我们继续讨论 [**返回-未定义**](https://palantir.github.io/tslint/rules/return-undefined/) 规则。这确保了当你的函数打算返回任何其他类型的值时，你将使用 return undefined 而不是 return。简单的例子？

```
// any type shouldn't be used, but anyway
function returnValue(valueToReturn: any, shouldReturn: boolean){
    if(shouldReturn){
        return valueToReturn;
    }
    return undefined;
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我需要返回 undefined，即使并不真的需要。另外，我知道这里我可以使用`shouldReturn`而不需要严格的未定义检查，因为它是布尔类型。

所以，这些规则可能看起来有点不直观，但是它们确实给你的代码增加了一些结构。

## 导出默认值

你知道 ES 模块吧？TSLint 对他们也有规定。 [**无默认导出**](https://palantir.github.io/tslint/rules/no-default-export/) 规则，因为我们正在讨论这一条，与 [**无默认导入**](https://palantir.github.io/tslint/rules/no-default-import/) 配对，有效地**禁止任何类型的默认导出和导入**。这迫使您只导出/导入代码的命名(分配给变量)部分，从而提高代码的可读性和自我文档化。但是，实际上，你可以使用默认的导出/导入，并在使用**一致命名**时获得类似的结果。

## 增量&减量

还记得上面的循环例子吗？记住 [**递增-递减**](https://palantir.github.io/tslint/rules/increment-decrement/) 规则，它将被认为是有问题的。

```
const numOfIterations = 10;
for(let i = 0; i < numOfIterations; i++){
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

而且都是因为`++`(也是`--`)运算符。递增/递减运算符经常出现在标准 for 循环中。你可能不知道，它既可以跟在它的参数后面，也可以跟在它的参数前面，两种语法有不同的含义**。考虑下面这个简单的例子:** 

```
let a = 1;
let b = a++;
let c = ++a;
console.log(a,b,c); // 3 1 3 
```

Enter fullscreen mode Exit fullscreen mode

通过了解输出，您可以推断出两种语法的含义。通过使用运算符作为字符后面的**，您**首先将变量`a`赋值给`b`和**，然后将** `a` **增加一个**。另一方面，当使用**之前的**操作符语法时，您**首先将`a`变量的值**增加 1，然后**将**赋值给`c`变量。乍一看，这似乎合乎逻辑，同样的逻辑也适用于减量运算符。****

但是，事实是这些特殊的语法差异经常会导致各种难以发现的问题。这就是为什么规则推荐使用不同的、更严格的语法:

```
let a = 1;

let b = a;
a += 1; // a++

a += 1; // ++a
let c = a;

console.log(a,b,c); // 3 1 3 
```

Enter fullscreen mode Exit fullscreen mode

我故意将这几行分开，以展示如何用不同的语法获得相同的结果。我想我们都同意，通过这种方式，与上面的语法相比，这段代码背后的思想更容易理解。然而，对于那些喜欢较短语法的人来说，这条规则似乎并不真正需要。

# 可推断的类型

现在，让我们深入研究更多的特定于 TypeScript 的规则和与之相关的问题。这里我们又有了另一个规则冲突。这一个更严重一点。我说的是[**typedef **](https://palantir.github.io/tslint/rules/typedef)vs[**不可推断类型**](https://palantir.github.io/tslint/rules/no-inferrable-types) 。我认为名字本身就能说明问题，但我们还是来解释一下这些规则吧。

```
const myNumber: number = 1;
const myString = "a"; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有两个简单的变量声明。两者有什么区别？(我不在乎不同的类型和名字😛)严格的**类型定义**。TypeScript 能够从变量的值中**推断出变量的类型**(至少在声明时赋值)。当然，您可以直接编写具体的类型，但是谁会关心这样的事情。写这些类型就是看起来不怎么干。

那么，有什么问题呢？是这两条规则的冲突。第一个声明满足了 **typedef** 规则的要求(该规则要求所有指定的构造都具有严格定义的类型)，但是当涉及到**不可推断类型**时就不那么满足了(该规则不接受不需要的严格定义)。在另一份声明中，情况完全不同。通过激活这两个规则， **tslint:all** 配置为您提供了这种选择的乐趣。😁

我对此有什么看法？嗯，一开始我想保留 typedef 规则，因为它非常严格。但后来我想这太过分了。另外，我碰到这样的事情:

```
const myArrowFunction = (arg1: number, arg 2: number) => {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

打开 typedef 的决定很容易做出。现在，问题是什么？在上面的例子中，我们有箭头函数。如你所知，这些只能像函数表达式一样，通过给变量赋值来定义(除非你已经用它创造了生命)。typedef 想要什么？为每个变量直接分配一个严格的类型定义。会是什么样子？

```
const myArrowFunction: (arg1: number, arg 2: number) => void 
= (arg1: number, arg 2: number) => {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我想即使是最严格的人也不会喜欢它的外观和感觉。选择很简单。然而，如果你想保持严格，你总是可以**深度配置**typedef 规则，只在特定的情况下要求严格的定义。老实说，我认为这种做事方式给你的代码带来了一些不规则性。但是，这只是我的看法。

## 界面

这只是一个简短的说明。在 TypeScript 中，有一种流行的做法是在所有接口名称的前面加上一个大写字母 **I** 。和...这也是有规则的！它被称为 [**接口名称**](https://palantir.github.io/tslint/rules/interface-name) ，并强制执行这种特殊的命名风格。虽然这清楚地将接口与其他构造区分开来，但至少在我看来并不直观。要知道，即使是官方 TS **lib.d.ts** 也没有采用这种做法(可能是因为其他 JS 文档兼容的原因，但反正是事实)，所以也不一定非要这样。

## 为...在&指数签名

这是我最不愿意抱怨的事情。🙃你有没有遇到过需要**迭代一个对象**键/值/等等的情况？？你是怎么做到的？我最常使用的是**...在**循环中，这可能是最流行也是最快的方式。

但是，在我向您介绍这个问题之前，让我解释一些打字稿的东西。首先，`keyof`操作符是所谓的索引类型查询操作符，这基本上意味着它为你创建一个类型，包括对象、接口等所有已知的属性。

```
const obj = {
    a: 1, 
    b: 2
}
type ObjKeys = keyof typeof obj; // "a" | "b" 
```

Enter fullscreen mode Exit fullscreen mode

我觉得很好理解。接下来，还有一个**索引签名**的想法。基本上，它允许你定义一个给定的类型，比如一个拥有某种类型(通常是字符串)的属性键的对象，只允许给定类型的值。所以，这就像是所有物业都要遵循的一般准则。

```
interface Obj {
    [key: string]: number;
}
type ObjKeys = keyof Obj; // string 
```

Enter fullscreen mode Exit fullscreen mode

另外，看看这个例子中的`keyof`输出了什么。它应该是**字符串**，就像我们之前定义的那样。

现在你知道了，让我提出问题。它与`keyof`类型、索引签名以及 for 之类的东西有关...在循环中，`Object.keys()` **和类似的**。考虑下面的例子。

```
interface Obj {
    a: number;
    b: number;
}
const obj: Obj = {
    a: 1,
    b: 2
}
for(const key in obj){
    if(obj.hasOwnProperty(key)){
        console.log(obj[key]) // error
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

问题是我们可以用 key 访问 obj 类型的 Obj，因为它没有索引签名！因此它会返回任何我们的配置不允许的值！为什么会这样？因为`key`的类型是**字符串**。所以，问题是...在循环中，以及任何其他相关的方法(如`Object.keys()`)使用字符串而不是`keyof`类型(这是更好的选择)来表示键的类型！你如何解决这个问题？当你试图访问值
时，通过改变`key`的类型

```
// ...
console.log(obj[key as keyof Obj])
// ... 
```

Enter fullscreen mode Exit fullscreen mode

据你所知，已经有许多关于这个特定问题的 [**GitHub 问题**](https://github.com/Microsoft/TypeScript/issues/24243) 公开了，但遗憾的是它们没有产生多少结果(至少这是我在使用 **TS 3.2.2** 时所知道的)。

您可以考虑只对键进行一次类型转换，并在循环开始时将其保存到一个变量中，但是让两个变量保存相同的值并很可能使用相似的名称并不太好。所以是的，这绝对是一个大问题。

和...那可能是最重要的最后一个。当然，还有一些其他更小的问题，但它们主要是关于我的编码风格，所以我没有在这里包括它们。😁

[![forest trees marked with question marks](img/9453751ad10800dc491f4711ffa65b9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wm4bWCbp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1458419948946-19fb2cc296af%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 又有什么意义？

正如我之前说过的，这篇文章的重点是**不要阻止**你使用 linters。只是给你一个警告，有一个好的配置是多么重要。并且从中获得乐趣和学习新的东西(TSLint 规则，打字稿之类的东西)。).您总是可以使用 **tslint:all** 配置(或者您选择的类似配置)然后禁用不需要的规则。只要确保**在做任何类型的项目(尤其是大项目)时使用棉绒**。😉

目前就这些。如果你喜欢这篇文章，可以考虑看看我的博客 上的最新博文。还有，[T5 在推特上关注我🐦](https://twitter.com/areknawo)和 [**详见我的脸书页面**](https://www.facebook.com/Areknawos-Blog-770620059945105) 。✌****