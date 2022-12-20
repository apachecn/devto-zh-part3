# FuseBox -以类型脚本为中心的代码捆绑器介绍

> 原文：<https://dev.to/areknawo/fusebox-typescript-centric-code-bundler-introduction-4g1>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

随着 JavaScript 能力和编写代码的复杂性的增长，一个伟大的转变朝着更加模块化的架构发展。CommonJS、AMD 以及最终标准化的 **ES6 模块** (ESM)清楚地表明了这一点。但是，在这种趋势下，今天的浏览器最好还是提供一个单一的代码包，而不是多个更小的模块。这就是为什么**代码捆绑**📦变成了一个非常受欢迎和普遍的任务。

JS 生态系统中有很多捆绑工具。主要的有，可以说， [**Webpack**](https://webpack.js.org/) ， [**Rollup**](https://rollupjs.org/) 和[**packet**](https://parceljs.org/)。所有这些都服务于相同的目的，但都有自己的特定目标。例如，Webpack 主要用于捆绑**网络应用**、汇总**库**和打包**原型**或快速完成工作。但是，许多这样的工具有一个共同点，那就是它们的**可扩展性**。可用插件、加载器等的数量。简直太棒了。另一方面，有些人可能喜欢像 Parcel 这样不需要太多配置的工具。包括我。就我个人而言，我更喜欢使用像 [**微束**](https://github.com/developit/microbundle)[**毕丽**](https://bili.egoist.sh/#/) 或 [**Poi**](https://poi.js.org/) 这样的工具——基于高度可配置和稳定的选项，但需要的**配置**要少得多。他们中的一些人更喜欢极简的方法，一些人只是尽可能地包含开箱即用的功能。尽管如此——我不在乎，因为它只是改善了我的体验，最终不会影响输出包的大小(希望如此)。🙃

不管怎样，今天我想和大家分享一个我最近才了解到的 bundler——[**fuse box**](http://fuse-box.org/)。也许你已经知道了？但是，对我来说，这真的是件新鲜事。而且，从一段时间的使用来看，我真的可以说这是我未来项目中值得考虑的选择...

# 什么是 FuseBox？

就像我之前说的，这是一个模块捆绑器。而是一种特殊的工具(每种工具都有其独特之处)。这并不是一个真正年轻的项目，它相当稳固，可以说相当受欢迎 **~ 3700 星**在 [GitHub](https://github.com/fuse-box/fuse-box) (如果你使用这种方法😅).以至于它甚至在我的 [VS 代码图标包](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)中有一个特殊的图标！无论如何，让我们快速概述一下它的特点。

首先，许多其他捆绑商都有。定制、**增量构建**和**缓存**——标准的东西。还支持 **ESM 动态导入**和 nice **插件系统**。

从特殊的东西，FuseBox 带有自动 CSS 分裂，代码优化， **HMR** ，和...一流的**打字稿支持**！没错！没有进一步的配置需要-只是交换你的*。js* 至*。ts* 扩展名(我说的是改变入口文件)仅此而已！

现在，这样的内置功能可能会让你想起 Parcel(如果你以前用过的话)。类似地，它还附带了 TS 支持、HMR、缓存等更多功能！但是，如果我错了，请纠正我，Parcel 感觉是针对 web 应用程序开发(而不是库)的，即使这样，它对于大多数原型开发来说也是相对较好的。它仍然是一个很棒的工具，只是我认为 FuseBox 配置更复杂，稳定性更好，是一个更好的选择。👍

# 我喜欢什么，我讨厌什么

现在，我知道我似乎对 FuseBox 完全有偏见。它只是给了我一些很好的第一印象。但是，尽管如此， **FuseBox 并不完美** -没有工具是完美的。还有很大的提升空间。因此，为了让您对这个工具有一个更全面的了解，让我来谈谈一些优点和缺点。

## 优点

FuseBox 绝对有很多令人喜爱的地方，这使它从其他捆扎机中脱颖而出。TypeScript 支持超快速构建，非常简单的配置是我的首选。FuseBox 使用 **CommonJS** 作为幕后的主要解决方法，让你的构建变得**闪电般**。⚡，我的意思是像毫秒一样快！哦，你仍然可以用你想要的方式使用 ESM。至于配置——非常非常直观！与大多数工具通常接受的标准配置对象不同，FuseBox 通过执行其**fuse . js**“config”文件，与 Node.js 一起运行。更像是 Node.js 程序。在这样的文件中，你可以使用 FuseBox **可链接**，非常 [**讨人喜欢的 API**](https://fuse-box.org/docs/development/configuration) ，由 TypeScript 自动完成功能支持。此外，甚至还有名为 [**Sparky**](https://fuse-box.org/docs/task-runner/sparky) 的内置任务运行器来提供附加功能！

但是，是的，也有一些缺点。对我来说，主要是 FuseBox 不能自己生成 TypeScript 声明文件。事实上，FuseBox 经常**在某种程度上忽略 tsconfig.json** 。当然，你可以单独使用 TSC 或 Sparky 来做这些事情，但许多捆扎机都内置了这个选项，这正是我想要的。

## 弊

接下来是它的**文档**。它很好，易于导航，易于理解，但是，当涉及到更复杂的事情时，我有一种感觉，它**并没有涵盖所有**。这些是我的主要选择。还有一个事实是，你必须手动安装 FuseBox 将使用的工具(为什么不是 all-in-one？-这是一个开发工具，对不对？).最后，还有**插件集**。它肯定没有 Webpack 或 Rollup 大。只有一些第三方插件，其余的随 FuseBox 包提供。那些有相当数量的 FuseBox 官方网站的 [**文档**](https://fuse-box.org/docs/plugins/babel-plugin) ，用起来相当舒服。但是，有一个插件可以说是最重要的-[**QuantumPlugin**](https://fuse-box.org/docs/production-builds/quantum)。这只是一种用于创建**生产就绪版本**的一体化插件。用起来还是不错的，但是相当臃肿，真的很复杂。以至于它仍然是一个庞大的文档，不能清楚地解释所有的事情。更糟糕的是，QuantumPlugin 并不是在所有情况下都有效(后面会详细介绍)。😕

总之， [**FuseBox v4**](https://github.com/fuse-box/fuse-box/tree/v4) 正在路上，对 **tsconfig.json 支持**、HMR 和其他东西进行了改进，所以我希望这个工具只会变得更好。现在，毕竟，我的这些抱怨，是时候最终弄脏我们的手，自己做一些**保险丝盒设置**！

# 保险丝盒用法

## 设置

### 基础知识

让我们从一个已经准备好的包开始，通过用 TypeScript 和 [**Terser**](https://github.com/terser-js/terser) 安装 FuseBox 本身以备将来使用。

```
yarn add --dev fuse-box typescript terser 
```

Enter fullscreen mode Exit fullscreen mode

现在，是时候规划我们项目的结构了。我们将使用 TypeScript 并将我们的条目文件(index.ts)放在 **src** 文件夹中。对于开发，我们希望启用**热重装**和**增量构建**。至于构建过程，我们将把我们的 bundle 文件输出到 build 文件夹，把 **TS 声明**和处理后的文件分别输出到**typing**和 **lib** 文件夹。有点标准的图书馆结构。📖

### 配置

首先，创建 **fuse.js** 文件并分析它的基本样板文件。

```
// fuse.js
const { FuseBox } = require("fuse-box");
const fuse = FuseBox.init({
  homeDir: "src",
  target: "browser@es5",
  output: "build/$name.js",
});
fuse
  .bundle("app")
  .instructions(" > index.ts")
fuse.run(); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们用 [`FuseBox.init()`](https://fuse-box.org/docs/development/configuration#initialisation) 方法初始化保险丝盒。在那里，我们传递我们的基本 [**配置对象**](https://fuse-box.org/docs/development/configuration) 和一些必需的属性。 [`homeDir`](https://fuse-box.org/docs/development/configuration#home-directory) 表示我们输入文件的主目录，，形式为`"[env]@[lang]"`，其中 env 可以是`"browser"`、`"server"`或`"electron"`，lang 具有语言级字符串的形式(如`"es6"`或`"esnext"`)。最后，我们用 [`output`](https://fuse-box.org/docs/development/configuration#output) 属性和一个有用的`$name`宏指定我们的包的输出位置，匹配我们的包的名称。

然后，我们利用 FuseBox 可链接 API，用`.bundle()`方法命名我们的 bundle，用`.instructions()`方法提供合适的 [**运行指令**](https://fuse-box.org/docs/development/instructions) (输入文件)。通过简单的`.run()`呼叫完成工作。

### 用法

您可以执行这样一个准备好的文件，就像任何其他 Node.js 程序一样——使用`node fuse.js`命令。**这么方便**！😉

```
node fuse.js 
```

Enter fullscreen mode Exit fullscreen mode

现在，要明确的是，在上面的配置中，我们应该已经包含了很好的 TS 支持...蛮**大捆**。默认情况下，FuseBox 使用 **CommonJS under-the-hood** (这就是为什么它这么快)。它只是将不同的模块包装在这些 **CJS 包装器**中，这些包装器可以快速打包。但是，这些额外的代码(和一些元数据)会导致您的最终包获得额外的 **5 KB** (未混合)大小。哎哟！

## 任务运行器

暂且不谈包的大小，我们还想在 FuseBox 管道中自动生成 TS 声明文件和输出(对于模块化环境)。而且，如前所述，没有内置的选项。所以，我们得用斯巴基来代替。

### 上下文

```
const { task, context, tsc } = require("fuse-box/sparky");
// ...

context({
    getConfig() {
      return FuseBox.init({
          homeDir: "src",
          target: "browser@es5",
          output: "build/$name.js",
      });
    }
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们必须改变 fuse.js 文件的基本结构。正如官方文档所建议的，在使用 Sparky 之前，我们应该首先设置一个所谓的 [**上下文**](https://fuse-box.org/docs/3.5.0/task-runner/getting-started-with-sparky#task-context) ，稍后我们的所有任务都可以访问它。而且，尽管它确实给我们的配置带来了一些清晰性，但我们很遗憾地在这个过程中失去了 TS 自动完成功能。😔

### 打造

在我们的上下文建立之后，它只是一个创建我们的任务的问题。在我们的例子中，我们将只使用两个。第一个用于构建过程，第二个用于开发。让我们看看它是怎么做的...

```
// ...
task("build", async ctx => {
    const fuse = ctx.getConfig();
    fuse
        .bundle("app")
        .instructions(" > index.ts");
    await fuse.run();
    await tsc("src", {
        target: "esnext",
        outDir: "lib/",
        declaration: true,
        declarationDir: "typings/"
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的`"build"`任务中，除了它的声明( [`task()`](https://fuse-box.org/docs/3.5.0/task-runner/sparky#task) 函数和**异步函数**)之外，我们在很大程度上遵循了与文件第一个版本相同的模式。只是在那之后，我们才使用 Sparky 友好地提供给我们的新的 [`tsc()`](https://fuse-box.org/docs/3.5.0/task-runner/sparky#tsc) 函数，以及它的 API 的其他方法。顾名思义，这个函数允许您使用提供的配置和本地 tsconfig.json 文件运行 TSC。遗憾的是，它要求 TypeScript 被**全局安装**才能运行。这是很严重的缺点！😠这里，我们为它提供了一个最小的设置，只输出我们构建的模块化文件和 TS 声明文件。

哦，还有我们这个 tsconfig.json...

```
{  "include":  ["src/**/*"],  "exclude":  ["node_modules"],  "compilerOptions":  {  "target":  "es5"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果 tsconfig.json 不存在，FuseBox 会用它的 [**默认配置**](https://fuse-box.org/docs/3.5.0/getting-started/typescript-project#typescript-configuration) 生成一个。除此之外，FuseBox 无论如何都会忽略`compilarOptions.module`属性，并将其设置为自己的 CommonJS 格式，因此设置该选项没有意义。请记住，在您的代码中，您应该只使用 ESM。

### 手表

至于我们的`"watch"`任务，我们在这里做着几乎相同的事情。只是这次不是只运行一次 FuseBox，而是使用它的[可链接](https://fuse-box.org/docs/3.5.0/development/watching) [API](https://fuse-box.org/docs/3.5.0/development/hmr) ，让 FuseBox 自动启用 HMR 和**文件为我们观看**。这就是我说的方便！

```
// ...
task("watch", async ctx => {
    const fuse = ctx.getConfig();
    fuse
        .bundle("app")
        .instructions(" > index.ts")
        .hmr()
        .watch();
    fuse.run();
}); 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们不会在这里运行 TSC。我只会减慢我们甜蜜的，几乎是瞬间的重建时间。

并且，要运行您的任何任务，只需在主命令后提供它的名称，就像这样:

```
node fuse.js build 
```

Enter fullscreen mode Exit fullscreen mode

当您在没有任何任务名称的情况下运行 FuseBox，并且定义了一些 Sparky 任务时，就会运行`"default"`任务。你可以用这个事实来创建你自己的自定义 [**默认任务**](https://fuse-box.org/docs/3.5.0/task-runner/getting-started-with-sparky#default-task) 和可用的 Sparky 的大量 API。如果没有提供这样的任务，FuseBox 执行不会做任何事情。

## 生产

### 路径

是时候来点最后的**制作小技巧**了！✨:当你想为多个目录使用一个配置时，你必须知道 FuseBox 有一些不同的解析系统。这就是为什么简单的`./`做不到这一点。由于 FuseBox 考虑到了`homeDir`、tsconfig.json `baseUrl`，或者配置文件的位置，所以很容易迷路(至少 IMHO 是这样)。这就是为什么，如果你想确保你引用的是当前正在处理的的**目录，只需使用 [`process.cwd()`](https://nodejs.org/api/process.html#process_process_cwd) 和 [**路径模块**](https://nodejs.org/api/path.html) 。这就是使用成熟的 Node.js 程序的神奇之处！** 

```
// ...
const fuse = FuseBox.init({
    homeDir: process.cwd(),
    // ...
});
// ... 
```

Enter fullscreen mode Exit fullscreen mode

### Globals

下一个- **全局**。特别是在为**浏览器环境**创建库时，创建暴露全局对象(浏览器中的`window`和 Node.js 中的`exports`)的某些属性的构建通常非常有用。为此，FuseBox 在其配置对象中提供了特殊的 [`globals`](https://fuse-box.org/docs/3.5.0/development/configuration#global-variables) 字段(或一个`.globals()`可链接方法)。有了这个属性，您可以公开多个包(引用 docs ),或者，更多的时候，只公开从您的条目文件中导出的包。在这种情况下，只需为你的全局对象提供选择的名称，并将其分配给`default`属性。

```
// ...
const fuse = FuseBox.init({
    globals: {
        default: "NameToExposeToWindow"
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 缩小

最后，我们有我们的包大小。这是事情开始变得有点...复杂。理论上，我们应该能够丢弃 [**TerserPlugin**](https://fuse-box.org/docs/plugins/terser-plugin) ， **QuantumPlugin** ，就像其他任何一种方法一样，然后就到此为止。🙌

```
//...
const { FuseBox, TerserPlugin, QuantumPlugin } = require("fuse-box");

const isProduction = process.env.NODE_ENV === "production";

const fuse = FuseBox.init({
    // ...
    plugins: [
        isProduction && QuantumPlugin({
            bakeApiIntoBundle: true
            treeshake: true
        }),
        isProduction && TerserPlugin(),
    ]
    // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用一种简单的方法在 FuseBox 中应用插件。首先，只有当我们处于**生产模式**时，我们才应用 QuantumPlugin。只是一个简单的检查，以节省开发时的一些时间(然后用`NODE_ENV=production`设置)。然后，我们用一个简单的调用和配置启动了我们的插件——就像其他插件一样。QuantumPlugin 使用一个 [**不同于前面提到的标准 CJS 包装器的 API**](https://fuse-box.org/docs/production-builds/quantum#features) 。它要小得多，但还不完全兼容原来的版本——这就是默认情况下不使用它的原因。它还应用了一些优化，例如**。我们的配置对象基本上启用了提到的树摇动特性，并将这个 API [**放入我们的包**](https://fuse-box.org/docs/production-builds/quantum-configuration#bakeapiintobundle) (默认情况下，它位于一个单独的文件中)。**

 **然后，我们放入 TerserPlugin 来缩小我们的包。值得注意的是，与标准的丑陋相比，Terser 支持**现代的 ES-Next 语法**即开即用的缩小。

上面的配置应该会显著缩小我们的输出大小。仍然会有一些样板文件，但是我们只讨论 300~400 字节。好像很完美！那么，有什么问题呢？🤔

嗯，我之前说过 FuseBox 对 web 应用和库捆绑有很好的支持...是啊，这就是第二次了。QuantumPlugin **不能很好地与标准分辨率方法中可用的所有功能**一起工作...而支持**的 globals** 就是其中之一。而且，尽管对任何类型的 web 应用程序来说都没什么大不了的，但是库开发却受到了很大的冲击。目前，唯一的解决方案就是在某些场景中不要使用 QuantumPlugin，只使用 TerserPlugin 或 [UglifyJSPlugin](https://fuse-box.org/docs/3.5.0/plugins/uglifyjs-plugin) 。然而，这仍然给我们留下了一些 KB 的 CommonJS 包装器...即使缩小了。我只是希望这个问题会在 **v4** 版本中得到解决。好像是个大的。

# 思想？

所以，你可以清楚地看到，FuseBox 是一个很好的捆绑器...而且肯定是值得被了解的。随着一些**额外的调整**，我真的对这个工具抱有很大的希望。功能集大部分已经完成，配置过程非常引人注目...只是有些事情需要改进。完成之后，谁知道呢？也许它甚至会与 Webpack 和 Rollup 的地位相匹敌？🤔

无论如何，感谢你阅读这篇帖子，并一如既往地让我知道**你对它和 FuseBox down **的**有何看法**！另外，在这里放下**一个反应**，在那里放下 [**一个明星**](https://github.com/fuse-box/fuse-box/stargazers) ，以示支持！想从这个博客中获得更多**最新内容**，可以考虑在 Twitter 上**关注我****， [**在我的脸书页面**](https://www.facebook.com/areknawoblog) 查看 [**我的个人博客**](https://areknawo.com) 。再次感谢您的阅读，下一集再见！🔥✌****