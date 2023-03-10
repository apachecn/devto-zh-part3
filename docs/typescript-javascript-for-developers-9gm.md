# TypeScript:面向开发人员的 JavaScript

> 原文：<https://dev.to/leolanavo/typescript-javascript-for-developers-9gm>

[![Unfaithul guy meme using TS and JS](img/95f06a49fb0bc28a9bdd5d85c422994d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---2DKr6hb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.devrant.com/devrant/rant/r_912484_JsRTo.jpg)

TypeScript 是为开发人员制作的 JavaScript，我不是以一种轻松的方式引用这段话，我是指这段文字的每一个字母。但是，我言过其实了，让我们先做一些基础工作。

根据官方网站(typescriptlang.org)，TypeScript 是“编译成普通 JavaScript 的 JavaScript 的类型化超集”，但这个短语是什么意思呢？我会一点一点地分解它

## 种类鳞次栉比，知识不集中

被类型化意味着，嗯，TypeScript 的变量有*个类型*并且在*编译*的时候被检查。类型一开始似乎会减慢开发速度，devs 会说这样的话:“我知道这个对象有名字，别烦我！”。但是随着您的代码库以及开发人员数量的增长，您可能会忘记一个 has object 有名称字段，而另一个没有，所以现在，您有两个选择:挖掘代码以发现一个对象的每个可能的字段；或者问问别人，从长远来看，这两种选择都不可行。在这种情况下，如果对象是类型化的，您可以很容易地检查它有什么或没有什么。关于类型的结论是:

`Types make your code more readable, therefore more maintainable`

类型有一个额外的正加法步骤，这是更广泛和更好的自动完成，因为代码直接告诉编辑器一个类型有什么或没有什么，编辑器可以告诉 dev 一个函数调用是错误的，或者你忘记了在构造函数中初始化一些属性。

## TypeScript 不仅仅是 JavaScript，还是一个超集

你知道在过去的几年里，你是如何在每个 JS 项目中安装`Babel`的，为了拥有一些便利的特性，比如析构函数，同样的事情也适用于 TypeScript。我们的朋友 TS 拥有 plain JS 和 Babel JS 所拥有的一切，甚至更多，比如只是提议的函数。下图解释得稍微好一点:

[![Venn diagram showing how JS overlaps in TS](img/83410ac49ee94e7d06d1da7e184a9ea9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V0lAsrCb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://leanpub.com/site_images/essentialtypescript/Introduction----superset.png)

## 一切都以 JavaScript 结尾

最终，TypeScript 编译成我们普通的旧 JavaScript，这意味着您编写的代码被转换成我们普通的旧 JavaScript，准确地说是 ECMAScript 3，这听起来可能不好，但事实并非如此。这种转变允许任何可以运行 ES3 的浏览器或云提供商能够运行您的代码，甚至 IE 也在此列表中。

## 奖金

围绕 TypeScript 的工具是惊人的。编译器接受许多现成的配置，甚至是路径映射，告别`../../../user.ts`和问候`src/entity/user.ts`，甚至是避免常见错误的规则，比如过度使用`any`类型。TSLint 是一个全面的工具，帮助开发人员标准化他们的代码，帮助您的代码库感觉像是由一个人编写的。

## 结论

TypeScript 最终对系统的最终用户没有帮助，从 JavaScript 迁移过来也不会有性能提升。但是开发人员在编码时会感觉到他们的生活质量得到了提升，有了类型检查、良好的 linter 和他们已经熟悉的类似语法，但是有了更好的包和整体的健全性。请告诉我你使用 TypeScript 的经历，它是否对你有帮助？