# Node.js 内部-外部模块 API 重新发现...

> 原文：<https://dev.to/areknawo/node-js-inside-out-modules-api-rediscovery-3h2g>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

自从 2011 年**[**第一次发布以来，Node.js**](https://nodejs.org/) 发生了巨大的变化，或者我应该说**彻底改变了** JavaScript 开发及其用例。能够用他们最喜欢的语言编写代码，并在服务器端运行，许多 web 开发人员很快注意到这个项目的巨大潜力。快进到 2019 年，Node.js 是整个编程市场上最受喜爱和使用的**运行时**之一。速度快(感谢 [**V8**](https://v8.dev/) )，可靠，好用。还有，自带 **[的 NPM](https://npmjs.com/) 包经理**📦它拥有世界上最大的开源库和工具**！这些事实本身就说明了 Node.js 有多受欢迎。🚀****

 **基于以上原因，在这个名为**“node . js inside-out”**的系列中！我们将探索这个项目的一切。我们就来说说**到底是什么 Node.js** 其实**是**和**它提供了**哪些 API。随着 NPM 和 Node.js 框架数量的增加，许多开发人员更喜欢它，而不是 Node.js 本身提供的底层东西。👏不要误解我的意思——使用各种工具使你的开发更愉快是没问题的。只是有时候，当你需要获得一些额外的性能或者想知道到底发生了什么时，最好还是回到基础上来。可悲的是，许多人在从 Node.js 开始时省略了这一步(与 web 开发不同——JS、HTML 和 CSS 是初学者的标准里程碑)，直接使用不同的**框架**，而没有真正理解它们或它们的真正潜力。

正如我所说的，随着这个介绍最终结束，我希望这个系列提供一个替代的、对初学者更友好的 **Node.js 超深度介绍**...可以接受的方式。😉所以，我希望你会喜欢它，并学到新的东西！

# node . js 到底是什么？

首先，一点理论...但不是真的无聊。😃Node.js 本身是 JavaScript 的一个运行时环境。是**开源**和**跨平台**。它的开发始于 2009 年，2011 年首次正式发布。背后的想法很简单——允许 JS 在不同于浏览器的环境中运行。没有什么是以前没有做过的，只是没有那么大的成功。今天，它的开发由 Node.js 基金会监督，并得到了大量贡献者的额外帮助。它被业内许多知名人士使用，并且似乎不会随着时间的推移而停止发展和改进。

作为运行时环境，Node.js 由 **V8** 提供支持🔋-开源 JS 引擎(市场上最快的)，最初由谷歌创造**。因此，它的工作方式类似于任何基于 **Chromium 的**浏览器。代码在**单线程**上的**事件循环**中运行。**异步 I/O** 允许它处理多个**并发操作**。⚡:这种方法有它的缺点，但它们通常与 JS 有关。**

Node.js 还提供了很多**API**供开发者使用。它们允许访问日常浏览器无法访问的功能。它们以**模块**的形式提供，作为处理核心功能的标准方式。它们的特性变化很大——从**文件系统**访问和**密码术**到 **C++插件**、**子进程**和 **V8 访问**。我们将在后面的系列文章中探讨每一个问题。👍

随着 Node.js 的快速发展，越来越多有趣的工具出现。凭借其健壮的架构，您可以创建**服务器端代码**、 **CLI 工具**、**实时应用**，这进一步意味着喜欢游戏、社交媒体和其他！当然，结合客户端也是可能的，利用你目前的知识，也可以用 JS 编写客户端！但我不仅仅指浏览器！基于 Node.js，受 node . js 启发或用 node . js 构建，类似于 [**Electron**](http://electron.atom.io/) 或 [**NativeScript**](https://www.nativescript.org/) 的工具出现了。基本上，他们所做的是允许你创建完全原生的桌面或移动应用...用 JavaScript！而且，依我看，这是 Node.js 成功的关键——one，**单一语言统治一切**！✊

# node . js API

我想提交本系列的其余部分来探索 Node.js 的不同 API。我们将从([**ECMAScript**](https://nodejs.org/docs/latest-v10.x/api/esm.html))[**模块 API**](https://nodejs.org/docs/latest-v10.x/api/modules.html) 开始。我认为这是一个足够好的开始选择。😃模块几乎无处不在，但是你可能会惊讶于它们在 Node.js 中拥有多少(可能)未知的特性。我们将在以后的文章中探索更高级的 API！另外，作为旁注，我在本教程的剩余部分使用的是 **Node.js v10.15.3** -最新稳定版 [**LTS 版**](https://nodejs.org/en/about/releases/) 。现在，让我们开始吧！🎉

# 模块

可能今天几乎所有的 web 开发人员都使用某种模块系统来更好地组织他们的代码。最流行的选项是 ES 模块(最新的标准)和 CommonJS 格式(Node.js 中使用的格式)。但是 Node.js 模块系统不仅仅是导入和导出东西。😅这就是我们将要探索的！

## CommonJS

我们先回忆一下 **CommonJS** ( **CJS** )模块格式的规则——Node.js 中的主模块，在 node . js 中，与其他任何前端的 TypeScript 或者基于 Babel 的工作流不同，模块是真实的东西。您的导入和导出在运行时被**解析——而不是在任何类型的传输步骤。你得到的基本上是一个真正的模块系统。😮这自然有其利弊。但是，移植仍然是一个很好的选择(特别是当，例如做微优化并且不想在运行时解析模块的时候),你可以很容易地使用 Babel 或者任何其他你想要的工具——无论如何！😉**

我猜很多人把 CJS 称为拥有`require()` **句法**的人。这是因为这个特殊的关键字可能是这种格式最容易识别的符号。

### 导入/导出

对于导出，您可以在处理对象时将特殊的`module.exports`属性赋值给相应的属性。对于第二个目的，您也可以使用`exports`对象——一个快捷方式。在分配单个值时，不要混淆这两个值——导出不能处理这样的东西！那是因为`exports`实际上是对`modules.exports`的引用，默认为**空对象**。

```
// module1.js
exports.numberValue = 10;
exports.stringValue = 'str'; 
```

Enter fullscreen mode Exit fullscreen mode

```
// module2.js
module.exports = () => {
   // code 
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，**箭头函数** ➡语法(和许多其他 **ES-Next 特性**)是 Node.js(和其他基于 V8 的浏览器)原生支持的**。**

导入可以使用众所周知的`require()`语法:

```
const module1 = require('module1.js');
const module2 = require('module2.js');

module1.numberValue; // 10
module1.stringValue; // 'str'
module2(); 
```

Enter fullscreen mode Exit fullscreen mode

我认为众所周知，上面的语法可以自由地用于导入核心 Node.js 模块(如 **fs** 或 **path** )，位于相对路径(`./`)， **node_modules** 目录中的模块，以及 **global** 中的模块。还有，你可以随意省略你的**。js** ，**。json** 或**。节点**(用于本地附件)文件扩展名。或者使用 **index.js** 文件作为文件夹的主文件等。只是和 JS 模块相关的普通东西。大多数时候，它被忽视了...⚡

### 糖纸&全局

以上都只是纯基础。你可以很容易地去使用它，而不需要任何进一步的理解。但是，在这个系列中，我们将深入挖掘！所以，我们想知道`require()`、`module`和`exports`到底是什么。

在执行之前，来自每个导入模块的代码被放入一个**包装函数**中🌯，大概是这样:

```
((exports, require, module, __filename, __dirname) => {
    // module code
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个需要理解的非常重要的概念，主要有两个原因:

1.  不同模块顶层作用域的所有看似全局的变量和其他用户定义的变量都被保留在有限的、**模块专用作用域**中。你要用`module.exports` / `exports`实际上**向外界输出一些东西**。📤
2.  这完美地向我们展示了我们的`require()`函数和`module`对象实际上来自哪里。它还向开发人员隐藏了函数包装器的事实，这是一种很好的**形式，看起来像是全局变量**。👍

也就是说，我认为这是一个探索我们的顶级包装器的真正参数的绝佳时机:

*   **出口**——只是引用`module.exports`(如前所述)；
*   **require()** -用于**导入模块**的函数。它有自己的一些附加属性:
    *   **缓存** -所有加载的模块都被**缓存的对象**(后面会详细介绍)；
    *   **主**——引用一个`Module` **对象**代表**入口模块**；
    *   **resolve()** -当使用带有相同参数的`require()`时，将**确切文件名**(完整路径)返回到模块将从中导入的文件:
        *   **paths()** -在定位所提供的模块时，返回通过搜索到的**路径的数组；**
*   **模块** -代表**当前模块**的对象(模块实例)的引用:
    *   **children** -给定模块中第一个**导入**的模块数组；
    *   **exports** -一个用于**从给定模块中导出**值的对象；
    *   **文件名** - **给定模块的绝对路径**；
    *   给定模块的 **id** - **标识符**。通常等于文件名(索引文件除外)；
    *   **已加载**——表示**模块是否已经加载**。当在不同的地方(即周期)使用多个需求时，这一点尤为重要。首先确保它们装载正确；
    *   **parent** -引用首先加载给定模块的模块；
    *   **路径** -定位给定模块时搜索的路径数组；
    *   **require()** -提供一种调用 require 的方法，就好像它来自给定的模块；
*   **文件名** -模块的**绝对路径**；
*   模块的**目录名** - **目录名**；

感觉有点像医生，是吗？😅希望不是坏事。我试图以一种比官方文档更容易理解、**更短、**更简单的形式提供它。主要的一点是要理解这些看似全球化的东西来自哪里，它们做什么。你很可能很少使用上面的任何属性。值得注意的例外(超越导入/导出语法)包括`__dirname`和`__filename`，许多初学者可能不知道它们来自哪里，它们代表什么。现在你知道了。😉

**模块**(故意大写字母)是上面提到的所有模块实例继承的结构。Node.js 也允许你以核心**模块**模块的形式访问它😂(`require('module')`)。它的用例甚至比上面的 API 更少，因为它只提供了两个额外的属性:

*   **builtin modules**-node . js 内置模块名称的数组；
*   **createRequireFromPath()**-允许创建一个**相对要求**解析为从提供的路径开始，例如文件夹。在不限制可读性的情况下，使用来自同一目录的多个导入时很有用；

正如你所看到的，上面的属性有它们非常非常具体的用例。因此，我认为它们更多的是内部属性，而不是通用属性。😅但是，如果您正在开发 Node.js 框架...谁知道呢？🤔

### 缓存

关于模块需要注意的最后一点是，它们是被**缓存的**。这对它们的工作方式和实际加载它们的**性能**有着巨大的影响。一旦加载，你的模块就不需要第二次重新加载。相反，将使用其缓存版本(存储在由`require.cache`引用的对象中)。这导致了性能的提高，但也有一些额外的，有时是理所当然的，**副作用**。你看，当一个模块第一次被加载(这就是为什么模块的子属性和父属性存在的原因，因为它们指示模块之间的特定关系，即它第一次被加载和缓存的位置)，缓存然后被访问时，它的所有代码已经被执行了一次**并且这个模块的所有导出被**携带在导入给定模块的所有文件**中。这允许一些巧妙的技巧，比如半全局的专用模块(可以在任何地方导入和更改的值，影响其他模块)。🛸**

当然，您可以通过搞乱`require.cache`对象并删除给定的模块(通过其 id)来强制重新加载模块。但是，**并不是真正推荐的**练习——除非你确定这正是你想要的。

## ECMAScript 模块

到目前为止，我们只讨论了 CJS 模块。但是，正如许多 web 开发人员现在应该做那样，2015 年，随着 **ES6** 的出现，出现了一个新的标准(不再是新的了，对吧？😅)简称为 **ECMAScript 模块**(简称 **ESM** )。是他们给我们带来了这个优秀的`import` / `export`语法，以及最终的**行业级标准**！可悲的是，正如我们已经看到的 Node.js 本身，旧的标准(CJS，AMD 等。)仍然在一些地方流行，甚至像 Node 这样积极发展地方。但是，随着引入 ESM 支持的**节点 v8.x** 的发布，这种情况终于发生了变化，尽管有一个**实验性标志** ☢(到目前为止一直保持到当前的 v11.x)。但是，这并不能阻止我们在 Node.js 中仔细研究 ESM，对吗？👍

### 启用

尽管实验状态可能不会困扰您(除了一些仍然需要实现或改进的特性)，但它也带来了一些额外的要求。ESM(在撰写本文时)不支持开箱即用。无论何时运行节点，都必须使用`--experimental-modules`标志来正确启用它们。还有，你得用**。mjs** 扩展名，您的文件可以通过 ESM 系统正确加载。👉

非常坦率地说，ESM 基本上是与 CJS 向后兼容的(除了一些 API 差异)，这意味着你可以通过 ESM 自由地导入 CJS 模块，没有太多的麻烦。另一方面，您不能用 CJS 语法导入 ESM 模块。这是不允许的，因为 CJS 使用**不同的解析方法**和**定时**(不向前兼容😅).当然， **JSON 文件**和 C++模块/ **本地插件**可以自由地与 ESM 语法一起使用。

### 差异

除了交叉兼容性之外，Node.js CJS 和 ESM 实现之间还有几个不同之处。ESM 有完全不同的**解析系统**，基于 **URL** 和**文件:协议**。这意味着你可以传递额外的**查询参数**来指示下一个模块应该被再次加载(而不是使用它的缓存版本)。💾

```
import module from './module1.js?id=1';
import moduleClone from './module1.js?id=2'; 
```

Enter fullscreen mode Exit fullscreen mode

目前，不能使用外部 URL。虽然有了上面的原理图，在不久的将来也许就有可能了。

URL 格式也用于标识缓存中的模块(这就是上面的例子起作用的原因)。但是，由于我们无法获得与 CJS 相同的价值(`require()`、`module`等)。)缓存对象是单独存储的。此外，与 CJS 不同，ESM **不解析 NODE_PATH** ，这进一步意味着无法导入全球安装的模块。

最后，在其当前状态下，`import`提供了它自己的一个属性。这是一个名为`import.meta`的对象，它也有一个名为`import.meta.url`的属性，表示当前模块的**绝对 URL** 。

```
import.meta.url 
```

Enter fullscreen mode Exit fullscreen mode

### 挂钩

Node.js ESM 的最后一个新特性叫做**加载器挂钩**。⚡顾名思义，这些钩子允许你**用自己的**自定义代码**拦截 ESM 模块的加载进程**。👏

有两种可能的挂钩供您使用- `resolve()`和`dynamicInstantiate()`。您可以在一个单独的 JS 文件中以**异步函数**的形式提供其中的一个或两个。稍后，您可以通过一个简单的 CLI 参数:
来加载和使用它们

```
node --experimental-modules --loader ./loader.mjs ./index.mjs 
```

Enter fullscreen mode Exit fullscreen mode

`resolve()`钩子有 3 个参数:

*   **说明符** -当前模块文件的**绝对路径**；
*   **parentModuleURL** - **父模块**的 URL(首先加载给定模块的那个)。它遵循 file: protocol，在入口模块上使用时默认为 undefined(没有父级)；
*   **defaultResolve()** -默认**解析函数**；

经过适当的处理后，您的解析钩子应该**返回一个具有两个属性的对象**:`url`和`format`。第一个指示为处理的模块(**文件:**)解析的 URL，第二个- **模块的格式**。📦虽然`url`很容易理解，但是`format`的字符串形式有 6 个可能的值:

*   **“esm”**-表示 ESM 模块；
*   **“cjs”**-表示 cjs 模块；
*   **“builtin”**-表示 Node.js 内置模块，如 http 或 path
*   **“json”**-表示 JSON 文件；
*   **“addon”**-表示一个 C++原生 addon；
*   **【dynamic】**-表示使用 dynamicInstantiate 钩子；

`dynamicInstantiate()`钩子允许你正确处理`"dynamic"`格式的模块。钩子本身是一个**异步函数**，接受一个`url`参数(被处理模块的 URL)，它应该返回一个具有两个属性的对象:

*   **exports** -导出属性的名称数组；
*   **execute()** -以上述导出为参数的函数。它应该访问先前在导出对象上定义的属性名，并使用。get()和。相应地设置()方法。稍后将在**模块评估**时执行；

一般来说，这个钩子给你一个选项，为需要的模块提供某种替代形式(例如，不同的文件扩展名)。请记住，它不必仅限于设置完全不同的属性——您可以使用提供的 URL 以您想要的方式加载和评估文件。一如既往的编程- **选项几乎是*无穷无尽的**！😉

# 我们才刚刚开始！

是的，已经有一段时间了，我们只设法覆盖了**模块 API** -仅仅是裸模块！看似如此简单的事情却有如此大的深度！🤔还是那句话，不要担心，还有更多**有趣的东西在库存中**！我计划接下来讨论**文件系统 API** (这是个大问题！)，但也许你想看看别的？我非常乐意接受不同的选择！请记住，我计划最终涵盖所有 Node.js APIs！

所以，请在评论中告诉我**你对这篇文章有什么看法，你接下来想看什么！另外，**把这个帖子**分享给其他 reach！😃一如既往，**在 Twitter 上关注我****[**在我的脸书页面**](https://www.facebook.com/areknawoblog) 关注我的 [**我的个人博客**](https://areknawo.com) 关注**关于这个系列的最新内容**和其他精彩的 JS 内容！🛸感谢你的阅读，我会在下一篇文章中见到你的！✌******