# 利弊和打字稿

> 原文：<https://dev.to/areknawo/the-pros-cons--typescript-3103>

JavaScript 开发最近在性能、特性、语法和开发舒适度方面向前迈进了一大步。但是，它仍有许多不足之处。编程体验有多好最重要的一个方面主要是编辑器支持(至少对我来说)。自动完成或智能感知(如果你对这些微软式的东西感兴趣)是我想关注的。面对现实吧，当无数的建议从你的光标下冒出来时，写点东西会更容易。

[![via Giphy.com](img/b48dcd94a9d30c1554b7a5e2bcba51db.png)](https://i.giphy.com/media/o0vwzuFwCGAFO/giphy.gif)

但是，正如你所期望的，也希望他们这样做，这些不可能只是随机的事情。这些都是基于类型的——这个术语在 JS 社区中不是很流行。如果你不知道这些是什么，那么谷歌一下，回头见。所以，你可能已经知道，类型是可怕的！(你可以同意也可以不同意，但我不在乎。)如果使用正确，它们可以为类型化语言提供编辑器支持和性能优化。但是，(至少据我所知)，JS 不是一种类型化语言，这是它的主要卖点之一。当然，非类型化语言有它们自己完全不同的优点，但肯定没有编辑器的支持。目前，如果没有类型，要实现良好的自动完成支持并不容易。因为 JS 在不久的将来不会成为一种类型化语言，所以出现了一些替代语言。其中之一是 TypeScript，一种语言，或者我可以称之为建立在 JS 之上的“瘦类型层”。这个创造，用一个漂亮的文字游戏创造的名字，目的是在不改变基本 JS 语法的情况下提供类似打字的语言体验。此外，它还增加了对 ES-next 中一些语法糖的支持。

## **如此...——“打字稿万岁！”-对吗？**

良好的...不完全是。你看，TS 从顶部看起来很棒——没有新的语法(当然，如果不算类型的话),编辑器支持(尤其是 VS-Code)以及与 JS 的“向后”兼容性。

[![via Giphy.com](img/98a8aa43bc4cbe484c5e84856e9a0b69.png)](https://i.giphy.com/media/10bdAP4IOmoN7G/giphy.gif)

你知道这意味着什么，对吧？

> **向后兼容性**是一个系统、产品或技术的属性，它允许与一个旧的遗留系统或为这样一个系统设计的输入进行**互操作**，特别是在电信和计算领域。

*维基百科*

在这种情况下，JS 不是旧的或遗留的——这并不重要。互操作性这个词是关键。在 TypeScript 中，您可以导入 JS 库，但是它显然不会神奇地为这个特定的代码生成类型。看起来可能不是这样，但是当你想在严格模式下使用 TS **“全潜能”**时，这就成了一个问题。然后你会得到一些漂亮的错误(除非你黑进你的方式通过 TS 配置)和没有类型安全，这是非常糟糕的。但是不要担心 **d.ts** (打字稿声明)文件在这里有帮助！当然，前提是你有时间做一些事情，比如为某个巨大的库编写 TS 绑定。你在这里并不孤单。和 [**打个招呼吧**](https://definitelytyped.org) 这里和你有同样需求的其他人已经覆盖了许多流行的 JS 库。可悲的是，并非所有(如此出乎意料)，这是主要问题。你可以什么都不做就打一些从一开始就不打类型的东西。但是除此之外，TS 类型系统缺少一些(不是真的需要，但是有帮助的)特性，那么是的- **“类型脚本万岁！”**而实际上，这个说法是 [**变成了现实**](https://octoverse.github.com/projects#languages) 。

## **其他选项**

在写这篇帖子的时候，打字稿的主要竞争对手只有两个——[**流**](https://flow.org)&[**JSDoc**](http://usejsdoc.org)本身。因为这与他们无关，所以我要说清楚:

### **流**

*   facebook 的好人做的 OSS
*   这是一种**类型检查器**而不是一种新语言；
*   具有与 TS 相似的打字系统；
*   大多数现代编辑器都支持；
*   性能有点滞后，但还好；
*   具有比 TS 更小的类型基；
*   比 TS 更好地支持**反应**；

### **JSDoc**

*   OSS 项目&一种文档标准；
*   以 JS 中**注释**的形式实现；
*   正常 JS 的性能没有下降；
*   与 TS / Flow 的分型系统不同；
*   可能比在 TS / Flow 中更难创建复杂类型；
*   几乎所有编辑器都支持；
*   额外文件生成器；

## **个人喜好**

就我个人而言，自从我发现了 TypeScript，我就无法停止使用它。它提供的编辑器自动补全支持对我来说是强制性的。我试着使用 Flow 和 JSDoc，但是老实说，它们并不比 TS 好多少(仅仅是我的观点)。当然，某些库缺少类型可能会有问题，但这并不是我可以责怪 TS 的原因。所以我的建议是？只要选择一个提到的项目，无论是 TypeScript、Flow 还是 JSDoc，如果你还没有，如果你有，那么不要忘记将这篇文章分享给那些还没有的人。祝你有美好的一天。