# 为什么我已经停止从我的 JavaScript 模块中导出默认值

> 原文：<https://dev.to/nzakas/why-ive-stopped-exporting-defaults-from-my-javascript-modules-2n7>

上周，我发了一条微博，得到了不少令人惊讶的回应:

> ![Nicholas C. Zakas profile image](img/fcb175951b1a2357b50e5fdf6bd50d2a.png)尼古拉斯·c·扎卡斯@ slicknet![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)2019 年，我要做的一件事就是停止从我的 CommonJS/ES6 模块中默认导出东西。
> 
> 导入默认导出已经变得像一个猜谜游戏，每次我都有 50%的机会出错。是一门课吗？是函数吗？2019 年 1 月 12 日下午 14:54[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1084101377297506304)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1084101377297506304)23[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1084101377297506304)141

在意识到我在 JavaScript 模块上遇到的很多问题都可以追溯到与默认导出的冲突之后，我发了这条微博。不管我是使用 JavaScript 模块(或者 ECMAScript 模块，因为许多人更喜欢这样称呼它们)还是 CommonJS，我仍然会在使用默认导出从模块导入时遇到困难。我收到了对这条推特的各种回应，其中许多人质疑我是如何做出这个决定的。这篇文章是我试图澄清我的想法。

## 几个澄清

就像所有的推文一样，我的推文是对我的观点的一个快照，而不是我整个观点的规范性参考。澄清人们在推特上困惑的几点:

*   知道导出是函数还是类的用例是我遇到的问题类型的一个例子。我发现命名导出为我解决的问题不仅仅是*问题。*
*   我遇到的问题不仅仅发生在我自己项目中的文件上，在导入我不拥有的库和实用程序模块时也会发生。这意味着文件名的命名约定不能解决所有问题。
*   我并不是说每个人都应该放弃默认出口。我是说在我写的模块中，我会选择不使用默认导出。你可能会有不同的感受，这没关系。

希望这些澄清设置了足够的背景，以避免在这篇文章的其余部分混淆。

## 默认导出:一个引物

据我所知，模块的默认导出最早是在 CommonJS 中流行起来的，模块可以像这样导出默认值:

```
class LinkedList {}
module.exports = LinkedList; 
```

这段代码导出了`LinkedList`类，但是没有指定模块消费者要使用的名称。假设文件名是`linked-list.js`，您可以在另一个 CommonJS 模块中导入该默认值，如下所示:

```
const LinkedList = require("./linked-list"); 
```

`require()`函数正在返回一个值，我碰巧将它命名为`LinkedList`以匹配`linked-list.js`中的内容，但我也可以选择将其命名为`foo`或`Mountain`或任何随机标识符。

CommonJS 中默认模块导出的流行意味着 JavaScript 模块被设计成支持这种模式:

> ES6 支持单/缺省导出风格，并给出了导入缺省值的最佳语法。
> 
> —大卫·赫尔曼[2014 年 6 月 19 日](https://mail.mozilla.org/pipermail/es-discuss/2014-June/037905.html)

所以在 JavaScript 模块中，你可以像这样导出一个默认:

```
export default class LinkedList {} 
```

然后可以这样导入:

```
import LinkedList from "./linked-list.js"; 
```

同样，`LinkedList` is 这个上下文是一个任意的(如果不是很合理的)选择，也可以是`Dog`或`symphony`。

## 备选:命名出口

除了默认导出之外，CommonJS 和 JavaScript 模块都支持命名导出。命名导出允许将函数、类或变量的名称传输到消费文件中。

在 CommonJS 中，通过将名称附加到`exports`对象来创建命名导出，例如:

```
exports.LinkedList = class LinkedList {}; 
```

然后，您可以像这样导入另一个文件:

```
const LinkedList = require("./linked-list").LinkedList; 
```

同样，我在`const`中使用的名称可以是我想要的任何名称，但是我选择了与导出的名称`LinkedList`匹配。

在 JavaScript 模块中，命名的导出看起来像这样:

```
export class LinkedList {} 
```

并且可以这样导入:

```
import { LinkedList } from "./linked-list.js"; 
```

在这个代码中，`LinkedList`不能是随机分配的标识符，必须与名为`LinkedList`的命名导出相匹配。这是这篇文章的目标与 CommonJS 的唯一显著区别。

所以这两种模块类型都支持默认和命名导出。

## 个人喜好

在进一步讨论之前，了解一下我自己在编写代码方面的一些个人偏好对你是有帮助的。这些是我应用于我写的所有代码的一般原则，不管我用的是什么编程语言:

1.  **显性超过隐性。我不喜欢有秘密的代码。某物做什么，某物应该叫什么等等。，只要有可能，就应该总是显式的。**
2.  **所有文件中的名称应该一致。**如果某个东西在一个文件里是`Apple`，我不应该在另一个文件里叫它`Orange`。一只`Apple`应该永远是一只`Apple`。
3.  尽早并经常抛出错误。如果有可能丢失某些东西，那么最好尽早检查，最好的情况是抛出一个错误来提醒我这个问题。我不想等到代码执行完毕才发现它没有正常工作，然后再去寻找问题。
4.  更少的决策意味着更快的发展。我的许多偏好都是为了消除编码过程中的决策。你做的每一个决定都会让你慢下来，这就是为什么像编码约定这样的事情会导致更快的开发。我想先决定一些事情，然后就走。
5.  **旁门左道减缓发展。每当你不得不在编码过程中停下来查找一些东西的时候，我称之为旁门左道。有时短途旅行是必要的，但是有很多不必要的短途旅行会让事情慢下来。我试着写一些代码来消除对副行程的需求。**
6.  认知开销减缓发展。简单来说:在编写代码时，你需要记住的细节越多，你的开发就越慢。

<aside class="note">The focus on speed of development is a practical one for me. As I've struggled with my health for years, the amount of energy I've had to code continued to decrease. Anything I could do to reduce the amount of time spent coding while still accomplishing my task was key.</aside>

## 我遇到的问题

记住所有这些，下面是我在使用默认导出时遇到的主要问题，以及为什么我认为在大多数情况下命名导出是更好的选择。

### 那是什么东西？

正如我在最初的推文中提到的，当一个模块只有默认导入时，我发现很难弄清楚我在导入什么。如果你正在使用一个你不熟悉的模块或文件，可能很难弄清楚返回的是什么，例如:

```
const list = require("./list"); 
```

在这种情况下，你期望`list`是什么？它不太可能是一个原始值，但从逻辑上讲，它可能是一个函数、类或其他类型的对象。我怎么能确定呢？我需要一次短途旅行。在这种情况下，副行程可能是以下任何一种:

*   如果我拥有`list.js`，那么我可以打开文件并寻找导出。
*   如果我不拥有`list.js`，那么我可能会打开一些文档。

无论是哪种情况，这现在都成为你大脑中需要的额外信息，以避免当你需要再次从`list.js`导入时的第二次侧行惩罚。如果你从模块中导入了大量的默认值，那么要么你的认知开销在增加，要么额外的工作量在增加。这两种方法都不是最佳的，而且会令人沮丧。

有些人会说，ide 是这个问题的答案，ide 应该足够聪明，能够判断出正在导入什么并告诉你。虽然我完全赞成用更智能的 ide 来帮助开发人员，但我认为要求 ide 有效地使用语言特性是有问题的。

### 名称匹配问题

命名导出要求消费模块至少指定它们从模块导入的内容的名称。好处是我可以很容易地在代码库中搜索到使用`LinkedList`的所有地方，并且知道它们都指的是同一个`LinkedList`。由于默认导出没有规定用于导入它们的名称，这意味着命名导入成为每个开发人员更多的认知开销。您需要确定正确的命名约定，并且作为额外的开销，您需要确保在应用程序中工作的每个开发人员将使用相同的名称来做相同的事情。(当然，您可以允许每个开发人员对同一件事情使用不同的名称，但是这会给团队带来更多的认知开销。)

导入一个命名的导出意味着至少在使用它的任何地方引用一个事物的规范名称。即使您选择重命名一个导入，这个决定也是显式的，并且在没有以某种方式引用规范名称的情况下是无法完成的。常见:

```
const MyList = require("./list").LinkedList; 
```

在 JavaScript 模块中:

```
import { LinkedList as MyList } from "./list.js"; 
```

在这两种模块格式中，您已经明确声明`LinkedList`现在将被称为`MyList`。

当命名在整个代码库中保持一致时，您可以轻松地完成以下任务:

1.  搜索代码库以查找使用信息。
2.  在整个代码库中重构一些东西的名字。

在使用默认导出和特定事物命名时，有可能做到这一点吗？我的猜测是肯定的，但我也猜测这会更加复杂和容易出错。

### 导入错误的东西

JavaScript 模块中的命名导出比默认导出有一个特别的优势，那就是当试图导入模块中不存在的东西时会抛出一个错误。考虑这个代码:

```
import { LinkedList } from "./list.js"; 
```

如果`list.js`中不存在`LinkedList`，则抛出错误。此外，像 IDEs 和 ESLint <sup id="fnref1">[1](#fn1)</sup> 这样的工具能够很容易地在代码执行前检测到缺失的引用。

## 差工装支持

说到 ide，WebStorm 能够帮助你编写`import`语句。 <sup id="fnref2">[2](#fn2)</sup> 当你输入完一个没有在文件中定义的标识符后，WebStorm 将搜索你的项目中的模块，以确定该标识符是否是另一个文件中的命名导出。此时，它可以执行以下任何操作:

1.  在缺少定义的标识符下面划线，并向您展示可以修复它的`import`语句。
2.  自动添加正确的`import`语句(如果您启用了自动导入)现在可以根据您键入的标识符自动添加一个`import`语句。事实上，当使用命名导入时，WebStorm 可以帮你很多:

Visual Studio 代码 <sup id="fnref3">[3](#fn3)</sup> 有一个插件提供类似的功能。当使用默认导出时，这种类型的功能是不可能的，因为您想要导入的东西没有规范的名称。

## 结论

我在项目中导入默认导出时遇到了几个生产率问题。虽然这些问题没有一个是不可能克服的，但是在编码时使用命名的导入和导出似乎更符合我的偏好。使事情变得显而易见并严重依赖工具使我成为一个高效的程序员，只要命名导出帮助我做到这一点，在可预见的未来，我可能会倾向于使用它们。当然，我无法控制我使用的第三方模块如何导出它们的功能，但我绝对可以选择我自己的模块如何导出东西，并且会选择命名导出。

如前所述，我提醒你，这是我的观点，你可能不觉得我的推理有说服力。这篇文章不是为了说服任何人停止使用默认导出，而是为了更好地向那些询问为什么我个人将停止从我编写的模块中导出默认的人解释。

## 参考文献

* * *

1.  [esling-plugin-import `import/named`规则](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/named.md)T3】↩

2.  [webstorm:JavaScript 自动导入](https://www.jetbrains.com/help/webstorm/javascript-specific-guidelines.html#ws_js_auto_import) [↩](#fnref2)

3.  [Visual Studio 扩展:自动导入](https://marketplace.visualstudio.com/items?itemName=NuclleaR.vscode-extension-auto-import) [↩](#fnref3)