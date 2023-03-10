# 删除您未使用的 CSS

> 原文：<https://dev.to/yashints/knock-out-your-unused-css-2o57>

未使用的 CSS 是大多数 web 应用程序在性能和页面加载时间方面遇到的问题之一。

## 免责声明

除了这篇文章中的第一个选项，大多数时候你需要一个工具和一些手动干预来安全地删除未使用的 CSS。这些工具在某种意义上是伟大的，它们会让你知道你所不知道的；模板文件中的哪些类没有被使用。

这些工具不能处理复杂的场景，比如当 JavaScript 在模板中添加 DOM 元素时。

在单页应用程序(SPA)中使用动态模板时，或者当您的模板根据服务器端代码的状态发生变化时，他们会遇到问题。

## 问题

正如我所说的，未使用的 CSS 是大多数 web 应用程序在性能和页面加载时间方面遇到的问题之一。

当你使用像 Tailwind 这样的 CSS 库，或者像 [Bootstrap](https://getbootstrap.com/) 或 [Material Design](https://material.io/design/) 这样的 CSS 框架的旧版本时，这甚至会变得更糟。

注意:大多数 CSS 框架已经转向模块化结构，你可以只导入你需要的部分，而不必包含整个包。

## 最佳和最安全的方法

在我看来，最好和最安全的方法是小心处理掉任何 CSS 文件或者你从 HTML 或者模板文件中删除的部分。当涉及到这样的技术债务时，不要懒惰或无知。如果你参与了一个绿色领域的项目，确保你不会在没有意识到哪些部分实际上被使用的情况下，从你正在寻找的地方复制粘贴大块的 CSS。

你可以通过以下步骤使用[谷歌浏览器](https://www.google.com/chrome/)中的[开发工具](https://developers.google.com/web/tools/chrome-devtools/)手动找到你未使用的 CSS:

1.  在 Windows 上使用`F12`或`Ctrl` `Shift` `J`或在 Mac 上使用`Command` `Option` `J`打开 Chrome DevTools。
2.  在 Windows 上按`Ctrl` `Shift` `P`或在 Mac 上按`Command` `Shift` `J`打开命令菜单。
3.  键入“覆盖范围”并点击“显示覆盖范围”选项
4.  单击“保险”选项卡上的“重新加载”按钮。
5.  从 Coverage 选项卡中选择一个 CSS 文件，这将在 Sources 选项卡中打开该文件。

使用左侧有绿色实线的 CSS 规则。红线代表的不是:

[![Unused CSS](img/b772d4c8b284da60473296a2c9c2e76b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fLdmcMJx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sz7en9eia838wrguypf1.jpg)

**警告:**仅仅因为一个规则没有在这个页面上使用并不意味着它没有在其他地方使用。理想情况下，您会检查所有页面的覆盖率，并结合结果以获得更好的概览。
T3】

## 工具

有时候你不能坚持☝🏼点因为各种原因。例如，您参与了一个棕色区域项目，或者代码库太大，无法及时重构和修复问题。

在这种情况下，您可能会寻找一些工具来自动化该过程，并在构建期间系统地进行清理。幸运的是，有很多工具可以帮助你。我将涵盖一些著名的，并在最后提到一个简短的名单😉。

## 1。纯化 CSS

PurifyCSS 是一个工具，可以分析你的文件，检查代码，找出哪些类没有被使用。大多数情况下，当你有静态的 HTML 文件时，这个工具可以消除几乎所有未使用的 CSS。

除此之外，它还可以在一定程度上支持单页面应用程序(SPA)。

### 设置

**独立**

您可以通过`npm` :
安装该软件包

```
npm i -D purify-css 
```

Enter fullscreen mode Exit fullscreen mode

还有一些基本用法:

```
import purify from 'purify-css'
const purify = require('purify-css')

let content = ''
let css = ''
let options = {
  output: 'filepath/output.css',
}
purify(content, css, options) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道，这对我没有帮助，你可能是对的。但这是最简单的形式。事实上在`purify`命令中，`content`和`css`参数可以是 [glob](https://github.com/isaacs/node-glob) 文件模式的一个`Array`。现在你看到了更大的图景，以及这是如何帮助你的。

现在让我们把它变得复杂一点。

**通过咕噜声**

首先你需要安装 grunt 包:

```
npm install grunt-purifycss --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

然后使用:

```
grunt.initConfig({
  purifycss: {
    options: {},
    target: {
      src: ['path/to/*.html', 'path/to/*.js'],
      css: ['path/to/*.css'],
      dest: 'tmp/purestyles.css',
    },
  },
}) 
```

Enter fullscreen mode Exit fullscreen mode

这甚至可以处理使用 JavaScript 添加了一个类的情况😍。所以这个会被拿起来:

```
<!-- html -->
<!-- class directly on element -->
<div class="button-active">click</div> 
```

Enter fullscreen mode Exit fullscreen mode

或者用 JavaScript:

```
// javascript
// Anytime your class name is together in your files, it will find it.
$(button).addClass('button-active') 
```

Enter fullscreen mode Exit fullscreen mode

或者更复杂一点的场景:

```
// Can detect if class is split.
var half = 'button-';
$(button).addClass(half + 'active');

// Can detect if class is joined.
var dynamicClass = ['button', 'active'].join('-');
$(button).addClass(dynamicClass);

// Can detect various more ways, including all Javascript frameworks.
// A React example.
var classes = classNames({
  'button-active': this.state.buttonActive
});

return (
  <button className={classes}>Submit</button>; ); 
```

Enter fullscreen mode Exit fullscreen mode

**警告**:purify CSS 的 Webpack 插件已被弃用。您将需要使用 Purgecss，我稍后将介绍它。

CLI

安装命令行界面:

```
npm install -g purify-css 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`-h` param:
查看帮助

```
purifycss -h

purifycss <css> <content> [option]

Options:
  -m, --min        Minify CSS                                   [boolean] [default: false]
  -o, --out        Filepath to write purified css to                              [string]
  -i, --info       Logs info on how much css was removed        [boolean] [default: false]
  -r, --rejected   Logs the CSS rules that were removed         [boolean] [default: false]
  -w, --whitelist  List of classes that should not be removed        [array] [default: []]
  -h, --help       Show help                                                     [boolean]
  -v, --version    Show version number                                           [boolean] 
```

Enter fullscreen mode Exit fullscreen mode

### 统计数据

这个图书馆能帮你大忙。它非常有效，适用于复杂的场景。但是正如我之前提到的，你需要有好的测试来发现在清理之后是否有任何事情被搞砸了。

> 就 Boostrap 而言，PurifyCSS 可以减少高达~ 33.8%的未使用 CSS。

## 2。Purgecss(纯文本)

Purgecss 是另一个移除无用 css 的强大工具。它可以作为开发工作流程的一部分。它附带了一个 JavaScript API、一个 CLI 和流行构建工具的插件。

### CLI

可以全局安装`Purgecss`，也可以使用`npx`。

```
npm i -g Purgecss 
```

Enter fullscreen mode Exit fullscreen mode

并使用它:

```
Purgecss --css <css> --content <content> [option] 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，API 与`PurifyCSS`非常相似，但是当然选项会有所不同。

### 通过 Webpack

你应该先安装插件:

```
npm i -D Purgecss-webpack-plugin 
```

Enter fullscreen mode Exit fullscreen mode

并将其添加到您的 Webpack 配置:

```
const path = require('path')
const glob = require('glob')
const ExtractTextPlugin = require('extract-text-webpack-plugin')
const PurgecssPlugin = require('Purgecss-webpack-plugin')

const PATHS = {
  src: path.join(__dirname, 'src'),
}

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.join(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: 'css-loader?sourceMap',
        }),
      },
    ],
  },
  plugins: [
    new ExtractTextPlugin('[name].css?[hash]'),
    new PurgecssPlugin({
      paths: glob.sync(`${PATHS.src}/**/*`, {
        nodir: true,
      }),
    }),
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

更多信息请参考[他们的文档](https://www.Purgecss.com/)。

## 3。UnCSS

UnCSS 是另一个帮助你移除不用的 CSS 的工具。但是这个工具有点不同，它将把你的文件加载到 [jsdom](https://github.com/jsdom/jsdom) 中，然后用 PostCSS 解析所有的样式表。

一旦完成，`document.querySelector`将过滤掉在 HTML 文件中找不到的选择器。最后，剩余的规则被转换回 CSS。

**注意:**我喜欢的这个库最好的一点是他们的[非官方服务器](https://uncss-online.com/)，你可以在那里粘贴你的 HTML 和 CSS，它会在线向你展示简化的 CSS。

### 安装

同样，你可以使用`npm`来安装它或者使用`npx` :

```
npm i -g uncss 
```

Enter fullscreen mode Exit fullscreen mode

可以将 UnCSS 与节点:
一起使用

```
var uncss = require('uncss')

var files = [
    'my',
    'array',
    'of',
    'HTML',
    'files',
    'or',
    'http://urls.com',
  ],
  options = {
    ignore: ['#added_at_runtime', /test\-[0-9]+/],
    media: [
      '(min-width: 700px) handheld and (orientation: landscape)',
    ],
    csspath: '../public/css/',
    raw: 'h1 { color: green }',
    stylesheets: [
      'lib/bootstrap/dist/css/bootstrap.css',
      'src/public/css/main.css',
    ],
    ignoreSheets: [/fonts.googleapis/],
    timeout: 1000,
    htmlroot: 'public',
    report: false,
    banner: false,
    uncssrc: '.uncssrc',
    userAgent:
      'Mozilla/5.0 (iPhone; CPU iPhone OS 10_3 like Mac OS X)',
    inject: function(window) {
      window.document
        .querySelector('html')
        .classList.add('no-csscalc', 'csscalc')
    },
  }

uncss(files, options, function(error, output) {
  console.log(output)
})

/* Look Ma, no options! */
uncss(files, function(error, output) {
  console.log(output)
})

/* Specifying raw HTML */
var rawHtml = '...'

uncss(rawHtml, options, function(error, output) {
  console.log(output)
}) 
```

Enter fullscreen mode Exit fullscreen mode

或者使用他们支持 Gulp、Grunt 和花椰菜的构建流程(不幸的是没有 Webpack😔).有关如何设置这些工具的更多信息，请参考[它们的文档](https://github.com/uncss/uncss)。

## 比较

现在让我们比较一下这些工具，看看它们的优缺点。

### 纯化 CSS

一些人认为 PurifyCSS 的最大问题是缺乏模块化。有人认为这也是它最大的好处🤷🏽‍♂️.它可以处理任何文件类型，而不仅仅是 HTML。它还可以找到使用 JavaScript 添加的选择器。

不幸的是，由于每个单词都被认为是一个选择器，这可能会导致很多误报，并使生成的 CSS 比它应该的要大一点。

### 清洗 css

Purgecss 通过提供创建提取器的可能性来解决上述问题。这是一个简单的函数，它获取文件的内容并提取其中使用的 CSS 选择器列表。

提取器可以用作解析器，它返回一个 AST(抽象语法树),并通过它查找任何 CSS 选择器。这就是`purge-from-html`的工作方式。您可以为每种文件类型指定要使用的选择器，从而获得最准确的结果。此外，您可以使用默认或遗留提取器，它将模仿 PurifyCSS 的行为。

也就是说，Purgecss 也有一些缺点。首先，你需要为像`Tailwind`这样的框架编写一个定制的提取器。另一个问题是，当您使用像 [primjs](https://prismjs.com/) 这样的语法高亮器时，在这种情况下，您必须使用一个名为`whitelistPatternsChildren`的属性将您的令牌类列入白名单。

需要考虑的另一点是 Purgecss 没有 JavaScript 文件的提取器。但是由于它的模块化，开发者可以为像 *Angular* 、 *React* 或 *Vue* 这样的框架创建定制的提取器。

### UnCSS

由于其 HTML 模拟和 JavaScript 执行，UnCSS 在从 web 应用程序中移除未使用的选择器方面是有效的。但是，它的模拟在性能和实用性方面是有代价的。

此时，如果不使用服务器端呈现，UnCSS 可能是删除未使用的 CSS 的最准确的工具。

## 其他工具

以下是您可以考虑的其他工具:

*   GitBit :一个在线工具，抓取你的根页面和其中链接的样式表。
*   未使用的 CSS:另一个在线工具，可以很容易地找到并删除未使用的 CSS 规则

## 总结

虽然这些工具在查找未使用的 CSS 方面确实很有帮助，但是每种工具都有自己的缺点，您需要小心不要以损坏的 UI 告终。

希望您已经对如何找到未使用的 CSS 并将它们部署到空间中有了一点点了解😁👇。

> ![StackBlitz profile image](img/8c68e842042d2ce5a3171051871bc0c0.png)@ stack blitz![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)部署...去太空？！？🚀🔥🤯[deployto . space](https://t.co/zb8jNPs5zs)2019 年 05 月 02 日上午 05:10[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1123816907881242624)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1123816907881242624)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1123816907881242624)