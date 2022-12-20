# 什么是 TypeScript？

> 原文：<https://dev.to/singhdigamber/what-is-typescript-jig>

这篇文章介绍了 TypeScript 以及它是如何工作的？
最早出现在 [positronX.io - Learn 全栈开发](https://www.positronx.io)

# 1 什么是 TypeScript？

首先，TypeScript 是一种编程语言。我们使用这种编程语言进行大规模的 JavaScript 应用程序开发。我们可以称之为 JavaScript 的类型化超集。或者换句话说，我们已经和打字稿打交道很久了！

关于 TypeScript 最好的一点是，您还可以访问 ECMAScript 最新版本的特性。您也可以使用超出该范围的功能。当您编译 TypeScript 时，您将能够生成跨所有平台安全的 JavaScript 版本。

TypeScript 3.1，最新版本待价而沽。在这个版本中，你会遇到许多令人兴奋的特性，包括可选的静态类型、模块、接口、类等等。多亏了 TypeScript，您将使用 JavaScript 的未来版本。

如何使用 NPM 和 Visual Studio 代码设置 TypeScript？

## TypeScript 是如何工作的？

TypeScript 需要一个能将 TypeScript 语法转换成标准 JavaScript 的编译器。这个编译器叫做 transpiler。Transpiler 被设计成将一种编程语言转换成另一种。

您会注意到 TypeScript 文件是附带的。ts 扩展。一旦 transpiler 编译了。ts 文件，您将获得。js 文件作为输出。

TypeScript 的另一个好处是它允许您访问常见的 JavaScript 框架和库。这是通过提供声明文件的无缝集成实现的。

## TypeScript 编译器简介

我们已经确定类型脚本传输器被称为传输器。当谈到 transpiler 最基本的功能时，它会在将源代码转换成合法的 JavaScript 代码之前对其进行类型检查。借助静态代码分析，编译器可以避免运行时错误。你可以在你的代码中犯各种各样的错误，比如把错误类型的参数传给打字错误。

编译器将抛出编译错误来通知相同的情况。您可以在实际执行代码之前纠正所有这些错误。这是一项极其重要的服务。如果你曾经处理过代码，你会知道边缘情况和逻辑错误会导致我们的应用程序在运行时崩溃。

多亏了 TypeScript，我们将在整个代码中以一致的方式使用类型定义。

编译器由不同的部分组成，帮助快速处理源代码。这些部分有助于使代码可预测。

### 解析器

解析器是一个极其重要和复杂的组件。解析器从 TypeScript 源文件和输入数据构建数据结构。并且该数据结构在这里被称为抽象语法树。解析器给出源代码的结构形式。这有助于分析我们是否在遵循语言的语法。解析器检查源代码，看它是否遵循正确的语法。

### 活页夹

让我们用一个例子来检验活页夹的工作原理。假设我们有一个同名的模块和一个同名的函数。在符号的帮助下，绑定器将这些命名的声明链接起来。它有助于类型系统向它灌输一些意义。

### 类型检查器或类型解析器

该组件生成类型诊断，检查语义操作，并为每个构造分配类型。

### 发射器

发射器从 d.ts 和产生输出。ts 文件。至于输出的文件类型，它可以是. js.map(源映射文件)、d.ts(类型脚本定义文件)或。js (JavaScript 文件)。

### 前置处理器

预处理器管理和解析文件间的引用。它借助/// <reference path="…/">或 import 来完成自己的目标。</reference>

### 使用 TypeScript Playground 将 TypeScript 转换为 javascript

如果您希望了解更多关于 TypeScript 编译如何工作的知识，您不必马上设置它。如果您希望试验类型脚本代码，也有同样的方法。TypeScript 团队开发了一个在线工具，允许您在线编译 TypeScript 代码。并且将 JavaScript 输出与源代码并排进行比较。TypeScript Playground 是该工具的名称。要访问这些工具，你只需访问 [TypeScript Playground](https://www.typescriptlang.org/play/index.html) 。

您将能够与他人共享您在 TypeScript Playground 上创建的代码段。咖啡师的游乐场。可以在这里进入。

操场的另一个好处是你可以访问内置的例子。你可以研究不同类别和复杂程度的代码片段。就像使用泛型一样。当您使用这些代码片段时，您将能够以更好的方式充实 JavaScript 和 TypeScript 之间的链接。

## 结论

当您使用 TypeScript 时，您将看到生产率的显著提高。开发者体验也会变好。您可以轻松地将 TypeScript 与现有的 JavaScript 集成在一起，只需很少的开销，甚至不需要任何开销。这种整合并不复杂。你需要记住的一点是，TypeScript 并不是 Angular 独有的。事实上，像 Vue 和 React 这样强大的框架已经开始感受到 TypeScript 的魔力了。

开发人员已经意识到 TypeScript 在帮助创建可伸缩、可持续和可靠的应用程序方面的强大功能。TypeScript 和 JavaScript 都在不断发展，但这两者并没有发生冲突。他们创建 TypeScript 只是为了增强和补充 JavaScript。TypeScript 永远不会取代 JavaScript。当我们展望未来时，我们可以看到两者具有相似的特性，TypeScript 继续作为静态类型的替代。