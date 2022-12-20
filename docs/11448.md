# 通过重构第 1 部分(共 2 部分)为 JavaScript 开发人员编写类型脚本

> 原文：<https://dev.to/azure/typescript-for-javascript-developers-by-refactoring-part-1-of-2-52jl>

#### 学习 TypeScript，取一个已有的 JavaScript 应用，移植到 TypeScript。

我创建了一个存储库，目的是向 JavaScript 开发人员教授 TypeScript。想象一下，如果您可以使用一种工具来自动为您找到缺陷，并在 bug 进入生产之前更容易地将其消灭！本演练演示了如何操作。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[JeremyLikness](https://github.com/JeremyLikness)/[TypeScript-from-JavaScript](https://github.com/JeremyLikness/TypeScript-from-JavaScript)

### 通过对现有 JavaScript 代码的一系列重构来学习 TypeScript。

<article class="markdown-body entry-content p-5" itemprop="text">

# 来自 JavaScript 的类型脚本

这是一个项目，您可以通过逐个提交来查看 JavaScript 代码到 TypeScript 的转换。

[@JeremyLikness](https://twitter.com/JeremyLikness)

背景说明:

*   1995 年:摩卡成为 LiveScript，10 天写成
*   1996 年:JavaScript 活了！
*   1997: ECMAScript 1 设定了标准
*   1999 年:ECMAScript 3
*   2005: jQuery“规范化 DOM”
*   2009 年:Node.js，ECMAScript 5
*   2012 年:打字稿(有很多欢乐)
*   2015 年:ECMAScript 2015(“和谐”)

## 介绍

`git checkout d65aed6`

目前，有一个小的 JavaScript“应用程序”,您可以使用命令运行它:

`node index.js`

有一些缺陷，如果您在 IDE 中浏览 JavaScript，您可能会也可能不会得到关于错误的适当提示。

## 转换为类型脚本

`git checkout 08f09e3`

JavaScript 是有效的类型脚本(有一些例外)，所以我们将从设置类型脚本开始。

`npm install --save-dev typescript`

向`package.json`文件添加两个编译脚本，用于编译和在手表上编译:

```
"scripts"
```

…</article>

[View on GitHub](https://github.com/JeremyLikness/TypeScript-from-JavaScript)

该存储库包含了与几个提交相关联的文档，以完成整个过程。如果你是一个动手型的人，并且想一头扎进去，请访问这个资源库，并立即开始！我录制了一个配套的视频系列来指导您完成每个步骤。

[![TypeScript logo](img/4045f6d72127b737f6971bb8be5baf1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--75uJvTBF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/320/1%2AX3sMn7ORs8WtyqlapH63dA.jpeg) 

<figcaption>打字稿</figcaption>

每个视频只有几分钟长，所以请随时将此页面设为书签，并经常回来。你可能已经从我的 Twitter 帖子中找到了这些视频。

> ![](img/d04887d50b7626acca9def32d5697006.png)杰里米·利克内斯·⚡️@杰里米·利克内斯![](img/4d9c44713c216584b3d48ff3455cbb68.png)我正在发布一个新的视频系列，用非常短(2 - 5 分钟)的剪辑来教授 [#TypeScript](https://twitter.com/hashtag/TypeScript) ，通过重构现有的 [#JavaScript](https://twitter.com/hashtag/JavaScript) 。它大约有 15 个部分。我将在这里发布每一步，并以一篇将这些片段整合在一起的文章结束。这里是回购:[github.com/JeremyLikness/…](https://t.co/h5IuIDB42s)2019 年 2 月 15 日上午 00:24[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096203599946801152)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096203599946801152)19[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096203599946801152)45

### 1。介绍

在这一步中，我引入了一个简单的 JavaScript 应用程序，它在使用 Node.js 运行时无法正常工作。我通过添加 TypeScript 编译器、初始化其配置并将`index.js`文件重命名为`index.ts`来开始向 TypeScript 的迁移。

[https://www.youtube.com/embed/6FEakIehnls](https://www.youtube.com/embed/6FEakIehnls)

> 注意:初始化 TypeScript 的配置文件的另一种方法是使用`npx`命令，就像这样:`npx tsc --init`

### 2。配置和扩展(Rest)运算符

更新了 TypeScript 配置以关闭对严格类型的要求。这将删除除一个错误之外的所有错误。这个错误恰好是 TypeScript 发现的一个 bug。为了修复这个缺陷，使用了一个“扩展操作符”来允许一个参数列表被传入并解析为一个数组。因为旧版本的 JavaScript 不支持 spread 运算符，所以 TypeScript 会自动生成兼容的代码。这一修复改进了应用程序，但仍有一些缺陷。

[https://www.youtube.com/embed/1GJy74OwOzc](https://www.youtube.com/embed/1GJy74OwOzc)

🔗[了解关于 tsconfig.json 文件的更多信息](https://jlik.me/e63)

🔗[了解更多关于“休息参数”的信息](https://jlik.me/e64)

### 3。类别和类型

使用 TypeScript 的一个主要好处是能够使用类和类型，这一点您可以从名称中猜到。我重构了代码，使用类代替函数构造函数，并用类型注释属性。这立即为我们揭示了另一个在下一步容易修复的 bug。

[https://www.youtube.com/embed/WXbOjEkoPDA](https://www.youtube.com/embed/WXbOjEkoPDA)

🔗[了解更多基本类型](https://jlik.me/e65)

🔗[了解更多课程信息](https://jlik.me/e66)

### 4。自定义类型

类重构揭示了一个命名不一致的属性。这可以通过更新构造函数以匹配其他代码中使用的名称来解决。接下来，定义一个自定义类型，为`contactType` : `mobile`和`home`声明两个可能的字符串值。这揭示了一个缺陷，当修复时，纠正电话显示逻辑。

[https://www.youtube.com/embed/0wpWbEKUge8](https://www.youtube.com/embed/0wpWbEKUge8)

🔗[了解有关自定义类型的更多信息](https://jlik.me/e67)

### 5。范围和“let”关键字

由于 JavaScript 中在范围内捕获变量的方式，一个 bug 浮出了水面。一个简单的变化是用`let`指定变量，而不是用增加复杂性的额外代码包装调用。然后，TypeScript 编译器知道如何管理旧版本 JavaScript 的范围，并传递给现代实现。

[https://www.youtube.com/embed/gXZq-CzzPo0](https://www.youtube.com/embed/gXZq-CzzPo0)

🔗[了解有关信函声明的更多信息](https://jlik.me/e9e)

### 6。λ表达式

任何熟悉 JavaScript 的人都遇到过准确理解什么是`this`的问题。默认情况下，作用域在函数级别，所以`this.x`在嵌套函数中有不同的含义。Lambda 表达式不仅简化了函数的定义，还捕获了变量访问的一致层次的外部范围。

[https://www.youtube.com/embed/8boMVNdXXkU](https://www.youtube.com/embed/8boMVNdXXkU)

🔗[了解更多关于“this”和箭头功能的信息](https://jlik.me/fa9)

### 7。字符串模板

在 TypeScript 中，与现代 JavaScript 一样，可以使用字符串模板进行插值。这为您提供了一种嵌入变量和计算输出表达式的更简洁的方式。TypeScript 将把它转换成旧版本 JavaScript 的字符串连接，并为现代目标利用新的语法。

[https://www.youtube.com/embed/2VTJO8YP6fM](https://www.youtube.com/embed/2VTJO8YP6fM)

🔗[了解更多关于字符串模板的信息](https://jlik.me/fbf)

### 8。泛型类型

泛型或“泛型类型”是一种开发/编译时特性，我喜欢把它看作是实现策略模式的语法。它包括为一个类型构建一个模板，当类型被解析时，这个模板打开了一个新的可能性世界。最终的 JavaScript 不包含任何符号或语法，但是正如您将在这个视频中看到的，泛型的使用可以帮助在缺陷被发送到产品之前快速捕获和修复缺陷。

[https://www.youtube.com/embed/R4gkIzZitOE](https://www.youtube.com/embed/R4gkIzZitOE)

🔗[了解更多关于泛型的信息](https://jlik.me/fcd)

### 9。具有泛型的自定义类型

为了简化代码，创建了一个使用泛型定义谓词的自定义类型。你可以把一个谓词想象成一个测试。给定一个项目`T`，它返回`true`或`false`。该类型可用于定义`find`函数中的第二个参数。结果呢？更易于阅读和维护的源代码，无需对生成的 JavaScript 进行任何更改。

[https://www.youtube.com/embed/9vs-LgJlfHQ](https://www.youtube.com/embed/9vs-LgJlfHQ)

### 总结

第一部分到此结束。希望到现在为止，您已经对 TypeScript 感到满意，并且可以看到它的一些直接好处。第二部分处理更高级的概念，包括接口，“关键类型”，类型保护，严格类型，类型装饰，以及当你针对不同版本的 JavaScript 时会发生什么。(当第 2 部分可用时，链接将在此处更新)。

问候，

[![Jeremy Likness signature](img/97adde39a566ae152324f60361115585.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u6zXBAsP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/197/1%2AKwXn3ElS7vnWIk8lNVhBsA.gif)

* * *