# 什么是 babel-preset-env，我为什么需要它？

> 原文：<https://dev.to/jakoblind/what-is-babel-preset-env-and-why-do-i-need-it-5dni>

如果你遵循了最近关于如何建立一个现代 JavaScript 项目的教程，你已经安装了 Babel，然后将 *babel-preset-env* 添加到你的*中。babelrc* :

```
{
  presets: ['@babel/preset-env']
} 
```

...一切正常。它会像你想的那样为你传输 ES6，然后你继续学习教程。

但是你刚刚用你的*做了什么。babelrc* 文件及其工作原理？

只是键入你在教程中读到的东西，而不真正理解为什么要使用它，这种感觉并不好。让我们看看 *babel-preset-env* 到底是做什么的，以及为什么你需要它。

这是我关于用 Babel 和 webpack 实现 ES6 的文章的第 2 部分。在第一部分中，你学习了巴别塔解决什么问题的基础知识。

在我告诉你 babel-preset-env 做什么之前，我需要向你解释一下 babel 的基础知识。即使你觉得你已经知道了，刷新一下也是好的。

## 巴别塔从无到有

使用 Babel 最常见的方式是与 webpack 等构建系统一起使用。当在构建工具中使用 Babel 时，很容易忽略 Babel 的实际工作方式。感觉 Babel 和 webpack 是一回事。

一个很好的练习是在没有构建系统的情况下学习 Babel 即使您不打算在生产中以这种方式使用它。因此，准备好您的编辑器和终端，开始编码吧。

首先创建一个新的 NPM 项目:

```
mkdir babel-test
cd babel-test
npm init -y 
```

接下来将 Babel 作为依赖项安装到您的空项目:

```
npm install --save-dev @babel/core @babel/cli 
```

添加一个名为 *input.js* 的文件，并在其中添加以下内容:

```
const hello = () => console.log("hello world!"); 
```

这是一个[箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，是 ES6 的一个功能。大多数浏览器都支持这一点，但是[并不是所有的浏览器都支持，这就是为什么把它移植到 ES5 是个好主意。现在让我们通过巴别塔运行这个文件:](https://caniuse.com/#feat=arrow-functions) 

```
npx babel input.js --out-file output.js 
```

如果你打开 *output.js* ，你会发现什么都没有改变。和 *input.js* 的内容一模一样。这不是你所期望的，是吗？这样做的原因是 Babel 不做任何开箱即用的事情，但 Babel 是建立在预置和插件之上的。如果你不添加任何预置或者插件，那么 Babel 什么都不会做。

> 巴贝尔不做任何开箱即用的事情

### 完成这项工作的是巴别塔插件

为了让 Babel 实际上*做*一些事情，我们需要添加一个插件。是插件做了繁重的工作。

每个插件都有自己的 NPM 库。所以对于每一个你想安装的插件，你必须安装一个新的 NPM 库，或者你可以使用一个预置，我们将在下一节回来。

在你用 *input.js* 编写的代码中，你只有一个 ES6 特性:一个箭头函数。所以我们现在要做的是安装一个 Babel 插件来转换 ES6 arrow 函数。

让我们从安装一个插件开始，该插件将您在 *input.js* 文件中编写的函数进行转换。

```
npm install --save-dev @babel/plugin-transform-arrow-functions 
```

接下来，您需要告诉 Babel 使用依赖项。添加一个名为*的文件。babelrc* 文件到项目的根目录。这是 babel 的配置文件，您将使用它来告诉 Babel 在进行移植时使用插件[@ Babel/plugin-transform-arrow-functions](https://babeljs.io/docs/en/babel-plugin-transform-arrow-functions)。这个插件只传输 ES6 箭头函数。

```
{
  plugins: ['@babel/plugin-transform-arrow-functions']
} 
```

好了，就这样。现在您可以重新运行 babel 并查看生成的 output.js 文件。它现在包含了编译好的代码！

```
const hello = function () {
  return console.log("hello world!");
}; 
```

太棒了。但是如果你想使用更多的 ES6 特性，你需要安装一个 NPM 包并在*中添加一个条目。babelrc* 为*的每一个*功能。那只是太多的工作。但是有一个解决方案:预置！

### 巴别塔预置捆绑在一起的常用巴别塔插件

巴别塔基金会已经创建了包含通用插件包的预置。这意味着你只需要做一次 NPM 安装和巴别塔配置，然后一堆插件就会自动为你安装。

有许多不同的巴别塔预设，既有来自巴别塔基金会的官方预设，也有来自其他组织如 [Airbnb](https://github.com/airbnb/babel-preset-airbnb) 的非官方预设。见鬼，如果你愿意，你甚至可以创建自己的预置。

官方预设为:

*   @babel/preset-env
*   @babel/preset-flow
*   @babel/preset-react
*   @babel/preset-typescript

每个预设都有自己的 NPM 依赖项，您需要安装和配置。好了，现在你*终于*准备好学习 babel-preset-env 了。

## 好了，现在巴别塔-预设-环境

让我们转换你的迷你代码库，使用 babel-preset-env 来代替@ babel/plugin-transform-arrow-functions

安装 NPM 依赖项:

```
npm install --save-dev @babel/preset-env 
```

接下来，在*的预设部分进行配置。babelrc* 文件

```
{
  presets: ['@babel/preset-env']
} 
```

现在，您已经安装了 babel-preset-env 的基本配置。开箱即用，你得到你需要的所有插件来传输所有的 ES6 特性！

但是巴别塔预设 env 比这个聪明。你甚至可以更好地控制应该安装哪些插件

## babel-preset-env 的独特卖点是你可以定义你支持什么浏览器

默认情况下，babel-preset-env 只安装你需要的所有 ES6 插件。但是这会使你的包膨胀。看看这个例子，现代的 ES6 是如何转换成旧的遗留 JavaScript 代码的

[![](img/d519e2f54f36760e999a1ef34c85babb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5nq0An4i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d4jyn86spyrm8.cloudfront.net/unnamed.jpg) 发自[本推文](https://twitter.com/jamiebuilds/status/1022568923726639104)作者 [@jamiebuilds](https://twitter.com/jamiebuilds/)

左边是 ES6 代码，如果您必须支持非常旧的浏览器，那么您可以在中间看到生成了多少代码。右边是支持不太老的浏览器所需的代码量。

在这个例子中，支持旧的遗留浏览器所需的代码量非常大。

所以如果你只使用默认的 babel-preset-env，你可能会以一个臃肿的包结束。对此我们能做些什么？

如果有一种方法可以告诉 Babel 不要使用所有的插件，而只使用 T2 真正需要的插件子集，那就太好了。这样一来，你的代码就很少被转换成臃肿的 ES5 代码。

如果你读了我关于这个主题的前一篇文章，你会了解到大多数现代浏览器已经支持大多数 ES6 特性。如果你看看你网站的谷歌分析，你可能会意识到 99%都是在现代浏览器上。所以也许你不必支持*所有已经存在的*老浏览器。

当你决定要支持什么浏览器时，你可以用 babel-preset-env 告诉 babel 你应该支持什么浏览器。您可以通过名为*的配置文件来实现。browserlistrc* 是[标准](https://github.com/browserslist/browserslist)的一部分，定义了你的应用程序支持哪些浏览器。Babel-preset-env 从这个配置文件中读取。

例如，你可以通过在你的*中添加以下内容来表明你不支持 IE。browserlistrc* 文件:

```
not ie all 
```

或者你定义只支持市场份额 0.25%以上的浏览器:

```
>0.25% 
```

如果你向 babel-preset-env 描述你支持什么浏览器，它可以使用更少的插件，这意味着更少的不需要的代码。

## 我的 browserconfig 应该是什么样子？

阅读你的谷歌分析统计(或者你正在使用的任何工具)来了解你的用户正在使用什么浏览器，然后使用[浏览器列表](https://github.com/browserslist/browserslist) [文档](https://github.com/browserslist/browserslist)来构建你的配置。

如果你不使用谷歌分析，那么一个好的默认设置是支持所有市场份额在 0.25%或以上的浏览器，就像这样:

```
>0.25% 
```

建议[而不是](https://jamie.build/last-2-versions)使用“最新的两个版本”。

您可以通过运行下面的命令
获得您的 browserlist 配置支持的所有浏览器的列表

```
npx browserslist 
```

它将在控制台中列出所有浏览器。

## 下一步是学习如何配置 webpack

正如我前面提到的，使用 Babel 最常见的方式是使用 webpack。既然你已经知道了 babel 是如何工作的，自然下一步就是开始研究 webpack。

我创建了一个电子邮件课程，每天给你一封电子邮件，只需要几分钟就可以阅读。五天后，您已经学会了如何构建一个完整的生产就绪型 webpack 应用程序。通过[注册](https://pages.convertkit.com/8eeb9f21b0/26777fab4c)立即获得访问权限。免费的！

→ [免费网页包电子邮件课程](https://pages.convertkit.com/8eeb9f21b0/26777fab4c) ←页