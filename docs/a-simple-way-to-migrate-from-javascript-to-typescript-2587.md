# 从 JavaScript 迁移到 TypeScript 的简单方法

> 原文：<https://dev.to/apium_hub/a-simple-way-to-migrate-from-javascript-to-typescript-2587>

如果你在过去的几年里一直在软件开发行业工作，很有可能你一直在从事 JavaScript 项目。现在，你已经厌倦了。因此，在这篇文章中，我想谈谈如何**从 JavaScript 迁移到 TypeScript** 。

不要误解我的意思，JavaScript 是一种非常灵活、易学易用的语言，拥有最大的社区之一。但是它也带来了一堆你最终会遇到的陷阱，比如每个实例的松散类型的“奇怪”行为。还有一些非常有趣的语言可以转换成 JS，因此可以在 JS 之上使用，比如 Dart、Elm 或一种广泛采用的 TypeScript(部分归功于 Angular ),等等。

## 如何轻松从 JavaScript 迁移到 TypeScript

在开始一个新项目时放弃 JS 要简单得多，在那里你不必担心诸如复古兼容性之类的事情，或者 app 在生产中的维护。在这种情况下，你可以尝试许多选择，并选择一个你最喜欢的。

然而，如果您必须继续在一个旧项目上工作，您可以开始从 JavaScript 迁移到 TypeScript，以一种“缓慢”的方式，一个文件接一个文件，或者只在您创建的新文件中添加 TypeScript。

这是可能的，因为 TypeScript 是 JavaScript 的超集，这意味着任何 JS 代码也是有效的 TS 代码(假设 TS 配置设置为与之兼容)。

现在，我将介绍一种向项目添加 TypeScript 的简单方法，不需要修改我们的 webpack、gulp 或任何构建系统的配置，也不需要部署系统。

假设您在项目中使用 npm 作为包管理器，我们首先需要做的是添加 TypeScript 作为依赖项(如果没有，您可以全局安装):

```
npm install --save-dev typescript 
```

注意:根据您的项目，您可能还希望为您依赖的其他库安装“@types”，对于每个实例，如果您有一个 react-redux 项目，您可能需要安装以下内容:

```
npm install --save-dev @types/node
npm install --save-dev @types/react
npm install --save-dev @types/react-dom
npm install --save-dev @types/react-redux
npm install --save-dev @types/react-router-dom 
```

之后，我们需要在项目的根目录下添加一个‘ts config . JSON’文件。该文件包含将 TS 转换为 JS 所需的编译器选项。为了使问题最少，使用下面的配置使 JS 代码与 TS 兼容:

```
{
 "compilerOptions": {
     "module": "commonjs",
     "sourceMap": true,
     "jsx": "react"
 },
 "exclude": [
     "node_modules"
 ]
} 
```

注意:您可能需要根据您的项目更改一些位。更多信息请点击。

现在，在您的 package.json 中添加以下脚本:

```
"tsc:w": "tsc -w" 
```

并运行它。它将运行一个监视器来传送所有文件。ts(或者。tsx)文件转换成常规文件。js 文件。此外，它将在与原始文件相同的路径下生成这些文件，因此您可能拥有的所有导入和所有构建过程仍将像以前一样工作，因为。ts 文件被完全忽略，而使用 transpilation 的结果。生成的文件结构如下:

```
file.ts
 ├── file.js
 └── file.js.map 
```

现在，我们需要做的就是创建我们的第一个。ts '文件，方法是更改我们要迁移到 TypeScript 的现有文件的扩展名，或者创建一个空白文件，在我们的应用程序中开始工作。

尽管如此，这个变化并没有带来太大的改变。我们仍然可以放入普通的 JS 代码，不需要从 TypeScript 提供的任何帮助。为了让 TS 强制我们实际输入代码，我们需要更改“tsconfig.json”文件。特别是，我们将关注两个编译器选项，它们将强制我们的代码被实际类型化:

```
"noImplicitAny": true,
"strictNullChecks": true, 
```

让我们想象一下，我们有一个简单的抵押贷款模拟器，它告诉用户，根据他的财务状况，抵押贷款是否可行。为此，我们将有一个函数，以某种方式检索用户的储蓄:

```
function getSavings() {
 //returns savings
 } 
```

以及决定抵押贷款是否可行的函数:

```
function concedeMortgage(homeValue) {
     const savings = getSavings();
     return savings / homeValue > 0.2;
} 
```

但是，为了让它实际工作，我们需要检查输入是否存在。以及它是否是一个数字。这同样适用于来自`getSavings`的返回值，因为他不知道那个函数的返回类型是什么。因此，我们的代码可能看起来像这样:

```
function concedeMortgage(homeValue) {
     if(!homeValue || !parseFloat(homeValue)) return false;
     const savings = getSavings();
     if(!savings || !parseFloat(savings)) return false;
     return savings / homeValue > 0.2;
} 
```

这看起来很可怕。

但是，如果我们激活了`noImplicitAny`编译器选项，就不再需要检查输入值和从`getSavings`返回的值是否是数字类型，所以函数看起来会像这样:

```
function concedeMortgage(homeValue: number): boolean {
     if(!homeValue) return false;
     const savings = getSavings();
     if(!savings) return false;
     return savings / homeValue > 0.2;
} 
```

这是一个改进，因为编译器会帮助我们避免一些错误和打字错误，不允许我们用字符串调用函数，例如:

```
concedeMortgage("foo") // ERROR! Argument of type 'foo' is not assignable to parameter type 'number' 
```

不幸的是，null 和 undefined 仍然存在于每个类型的域中，因此这样做是可能的:

```
concedeMortgage(null) // Still works 
```

为了解决这个问题，我们需要激活我们提到的 tsconfig.json 文件中的另一个选项:“strictNullChecks”。

现在，用 null 调用函数，最终会被编译器捕获:

```
concedeMortgage(null) // ERROR! Argument of type 'null' is not assignable to parameter of type 'number' 
```

也就是说，代码不需要检查空值，将逻辑简化为:

```
function concedeMortgage(homeValue: number): boolean {
     const savings = getSavings();
     return savings / homeValue > 0.2;
} 
```

如果您将项目从普通 JS 迁移到 TypeScript，这只是对 type script 所能提供的一点小小的了解。

如果你想了解更多关于如何从 JavaScript 迁移到 TypeScript 的信息，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月时事通讯。

一个简单的从 JavaScript 迁移到 TypeScript 的方法的帖子[首先出现在](https://apiumhub.com/tech-blog-barcelona/migrate-javascript-to-typescript/) [Apiumhub](https://apiumhub.com) 上。