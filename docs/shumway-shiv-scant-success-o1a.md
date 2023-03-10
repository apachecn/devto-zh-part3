# Shumway Shiv:很少成功

> 原文：<https://dev.to/elmuerte/shumway-shiv-scant-success-o1a>

不久前杰梅因·奥彭问道

[![graphicbeacon image](img/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## 你的第一个网站设计是什么样的？

### jermaine Oppong 12 月 29 日 181 分钟阅读

#discuss #beginners #webdev](/graphicbeacon/what-did-your-first-website-design-look-like-1oj9)

我的第一个网站，从 1998 年到 1999 年，有相当多的 Adobe Flash 内容，大部分用于导航。在替换它的网站上，我仍然有一个我的第一个网站的镜像。所以人们可以回到过去。一些浏览器仍然有点支持 Flash，但这在将来会改变。所以最终我的第一个网站的镜子会被打碎。

所以我可以做几件事:

1.  什么都没有，让它最终死去。
2.  重新创建当前闪存中的内容。
3.  尝试以某种方式转换 Flash 内容。

显然第二点是不可能的。这是旧址的一面镜子，它的存在只是出于情感上的原因。因此，我打算尝试第三种方法。

# Mozilla Shumway

6 年前，当 Flash 还在流行网站上被广泛使用时，Mozilla 启动了 Shumway 项目。Shumway 基本上是一个用 JavaScript 编写的 Flash 播放器，利用了最新的 HTML5 技术。它的主要目的是作为 Firefox 的扩展，但它也可以作为独立的脚本在网站上使用。

大约三年前，这个项目的开发停止了。

# 输入 Shiv

尽管 Shumway 的开发停止了，但这并没有使创造立即无用。由于我的旧网站使用的是 Adobe Flash 4，而不是最新最棒的，没有什么奇特的功能。Shumway 很可能会在内容的渲染方面做得很好。

上周我决定试一试。制作一个简单的 JavaScript 库，我可以将它注入我的旧网站，它会用 Shumway 替换所有嵌入的 Flash 电影。

# 不是现代前端开发者

自 1998 年以来，我一直在研究 web 技术。但是在过去的几年里，我主要从事后端技术。而现代 JavaScript 前端技术却没有这么多。但是我知道我的香草，这就是我制作刀所需要的。

当然，我会尝试使用当前的前端技术创建这个项目，并将其打包。后端不再是唯一拥有强大构建工具的地方。

# NPM，不是吗！

那么我们从哪里开始呢？我想创建一个简单的单文件 JavaScript 库，将其与 Shumway 发行版捆绑在一起，并制作一个可分发的存档。

我试图寻找最好的、当前的方式来开始这样一个项目。除了创建您的`project.json`文件的`npm init`动作之外，我找不到任何有用的东西。我可以找到很多关于如何使用某种框架为网站创建项目的教程。但那不是我需要的。

## 我的文件放在哪里。

在 Java 世界中，我们有 maven，它定义了一个标准的项目布局。

*   科学研究委员会
    *   主要的
        *   Java 语言（一种计算机语言，尤用于创建网站）
        *   资源
    *   试验
        *   Java 语言（一种计算机语言，尤用于创建网站）
        *   资源
*   目标

但是我找不到前端/节点世界的标准约定。看一些 JavaScript 项目，文件似乎只是放在开发人员想放的地方。

我发现的唯一常见的布局是主 JavaScript 文件通常被称为`index.js`，测试通常被放在`test`目录中。

# 依赖关系

我只有一个非开发依赖项:Shumway，它似乎在 NPM 回购协议中。完美！

但也不尽然。看来，在 NPM 的依赖不是最新版本的 Shumway。尽管最新版本已有 3 年历史，但一个更老的版本出现在 NPM 回购协议中。

这个小细节让我花了很多时间去调试为什么一些 Flash 电影完全没有结果。

能够使用依赖关系管理到此为止。我猜我将不得不下载最新版本的 Shumway，并将其存储在我的项目中的某个任意目录中。

# 测试

是时候测试我的代码了。只需在浏览器中打开我的测试 HTML 文件...它不起作用。Shumway 需要的 JavaScript 功能不允许通过 de `file://`协议运行，所以我需要在 web 服务器上运行它。

这不会太难，安装并启动[服务](https://zeit.co/blog/serve-7)即可。我猜不会。

默认情况下，服务器强制执行“干净的网址”。这意味着`http://localhost:5000/test/example`实际上指的是文件`test/example.html`。但是出于某种奇怪的原因，他们也通过将`http://localhost:5000/test/example.html`重定向到`http://localhost:5000/test/example`来强制执行。这并不是一件坏事，只是它们的重定向代码被破坏了，并删除了位置的查询部分。此行为破坏了 Shumway 查看器，该查看器期望 Flash 文件作为查询参数加载。所以`"cleanUrls": false`在我的`serve.json`里。

在我的测试 HTML 中，我直接链接到了`/src/index.js`，但是在那个脚本中，我希望 Shumway 播放器的文件在`./shumway-dist/`中，所以在`/src/shumway-dist`中。但是因为这些文件并不是我项目源代码的一部分，所以它们存在于其他地方。(最初在我发现过时版本之前，它们位于 node_modules 目录中。)

幸运的是，Serve 可以选择将请求重写到不同的位置。关于这一点的文件是相当模糊的。我会假设一个简单的重写，比如

```
{  "source":  "/src/shumway-dist/**",  "destination":  "/build/shumway-dist/"  } 
```

会将对`/src/shumway-dist/foo/bar/quux.js`的请求重写为`/build/shumway-dist/foo/bar/quux.js`。但事实显然并非如此。我想不出一个合适的单线配置来重定路径。所以我最终有了这个怪物:

```
{  "source":  "/src/shumway-dist/:x/:y",  "destination":  "/build/shumway-dist/:x/:y"  },  {  "source":  "/src/shumway-dist/:x/:y/:z",  "destination":  "/build/shumway-dist/:x/:y/:z"  } 
```

它可以工作，但是我不太喜欢它。

# 包装

这个 JavaScript 库的想法是，用户应该能够简单地将它上传到他们的 web 服务器，并在需要它的 HTML 页面中包含一个`<script ... >`。所以我想把我的 JavaScript 文件和 Shumway 发行版文件打包到一个可下载的工件中。这个库不打算被其他 JavaScript 包用作依赖项。

Webpack 听起来像是我需要使用的工具。

> webpack 是一个模块捆绑器。它的主要目的是捆绑 JavaScript 文件以便在浏览器中使用，但是它也能够转换、捆绑或打包...

我只需要告诉它把我的`src/index.js`复制到`shumway-shiv.js`并从`shumway-dist`包中复制一些文件。然后生成一个漂亮的 zip 存档，我可以把它作为发布包上传。

为此，我显然需要在配置文件中编写更多的 JavaScript。

做第一部分是开箱即用的。但是要将文件从 shumway dist 页面转移到输出目录，需要我编写 CopyWebpackPlugin。我不希望在配置文件中写`new CopyWebpackPlugin(...)`。

为了创建 ZIP 文件，我必须编写 ZIP webpack 插件。这很简单，只是我还没有弄清楚我的输出 zip 如何包含我在 package.json 中定义的版本号。

运行 webpack 欢迎 be 关于文件大小的意见。是的，Shumway 包含一些大文件。我知道，我不会为此做任何事。所以`performance: { hints: false }`。

等一下...我`shumway-shiv.js`里的这些东西是什么？是网络包加载器。尽管这个输出文件只包含一个 JavaScript 源代码，没有其他内嵌的依赖项，但是加载器在那里。我以后会想办法摆脱它。

# NPM 明面上，或缺其中

我现在已经启动了一个测试 HTTP 服务器，Webpack 来打包它(和 eslint)。但是不用手动调用所有这些工具，我应该能够简单地从 npm 调用这些工具，对吗？在 Java 世界中，我会简单地调用`mvn package`来编译、测试和打包我的项目。

但是 npm 似乎缺乏建设项目的概念。相反，我必须在我的 package.json 中定义“脚本”，然后我可以用`npm run test`、`npm run package`执行这些脚本。但是这些脚本之间没有定义依赖关系。

如果我想要这样，那么我应该使用类似于 [Grunt](https://gruntjs.com/) 的东西。在这里我必须再次编写一个配置文件。基本上，它是 Java 的 Ant 的 NodeJS 等价物。(这些大的 JSON 文件并不比 XML 文件更易读。)

# 我的网站在颤抖

是时候上传我的新作品了，看看它如何让我的旧网站在没有 Flash 的浏览器上运行。

挺管用的。Flash 内容已被 Shumway 查看器正确替换。但是结果不尽如人意。

*   各种链接打开一个新的标签页，而不是在右边的框架中打开。
*   鼠标悬停替换无法正常工作。原来的内容没有了，但是新的内容没有显示。
*   内容没有缩放到最大大小。本应全屏显示的内容以原始大小显示。缩小规模似乎可行，但扩大规模却不行。
*   没有对齐。一切都呈现在左上角，而不是居中。

鼠标悬停行为的破坏是相当大的交易破坏者。所有这些工作，最终结果并不是我真正想要的。如果我的测试用例使用了网站上的一些 Flash 电影，而不是 Shumway 项目中的例子，我可能会更早发现这一点。

我可以调查其他人是否在一个分叉的 Shumway 项目中解决了一些问题。到目前为止，看起来像是*#3 试图以某种方式转换 Flash 内容。“已经失败了。

这个实验的结果就在我的 GitHub 上。欢迎在评论中发表任何关于我应该做什么，或者应该读什么，以使这个前端项目更好地工作的评论。