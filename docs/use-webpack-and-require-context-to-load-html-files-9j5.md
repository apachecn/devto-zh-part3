# 使用 Webpack 和 require.context 加载 HTML 文件

> 原文：<https://dev.to/kendalmintcode/use-webpack-and-require-context-to-load-html-files-9j5>

[![Glass fragments](img/41368945e5587ff005d53d1cddbb5ee0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s-v9ctU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/e765024447c2dc8c8275e065c154fbb8/3c94f/malcolm-lightbody-668615-unsplash-md.jpg)

现在有很多方法可以使用模板和模板语言来建立一个网站:你有把手、小胡子、markdown、jasmine、JSX，还有很多静态生成器来处理它们，比如 Eleventy、Hugo、Gatsby。更不用说选择普通的 HTML 或者某种框架，比如 React。

但是有时候你只需要从一些文件中抓取一些 HTML，然后放到页面上。在这篇代码文章中，我将向您展示如何使用 Webpack 和`require.context()`从文件夹中加载 HTML 片段，并将它们输出到页面上。

如果你只是想[跳到我们开始使用 require.context](#webpack-to-the-rescue) 加载 HTML 文件的地方，那就继续吧。

[![cube](img/7b0313e0240b0a4774bbc7dfb20815b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_b_pe_k_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1529700215145-58542a1f36b6%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

照片由[克里斯蒂安·弗雷南](https://unsplash.com/@christianfregnan?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

### 为什么要使用 require.context 或者 HTML 片段？

如果你想建立一个网站，甚至是一个小的网络应用程序，那么可能有更简单、可扩展的方法——参见本文开头提到的众多选项。

然而，最近，我一直在构建一个组件 UI 库(使用令人惊叹的[布尔玛 CSS 框架](http://bulma.io/))，并希望有一种快速的方法来简单地抓取一些 HTML 并将其包含在一个结束页面(或一系列页面)中，以显示该库的活动版本(有点像[布尔玛自己的文档](http://bulma.io/))。

然而，HTML 文件并不是完整的文档，只是 HTML 的*片段*，封装了库中的特定组件以显示其标记。此外，我想把它们分解成单独的文件放在一个文件夹中，以便更好地组织项目。这提出的主要问题是找到一种可靠的方法来抓取*所有的*文件，因为它们是被分解的部分，而不是整个 HTML 文档，并以逻辑顺序抓取它们，而不是必须有一个 10000 长的文档。

我使用 Webpack 捆绑所有东西，并处理 CSS 和 JS 处理，因此尝试找到一种简单的方法让 Webpack 获取这些文件并处理它们是有意义的。当然，我可以使用某种模板语言，但是这会导致一些问题:

*   在一个简单的站点中引入了另一个层次的复杂性(无论多小)。
*   不得不用模板语言重做组件。

当然还有其他选择，但有些并不完全可行...

### 使用原生 HTML 包括

实际上并没有。有很多涉及`iframe`和`object`的解决方案，但是没有一个是理想的——特别是考虑到使用 iframes 的缺点。

### 使用闪亮的新 HTML 导入功能

Eric Bidelman 有一篇关于使用 HTML 导入的精彩文章。他的方法使用来自 W3C 的当前[编辑器草案规范，使用文档头中的`link`元素导入 HTML 文件，如下:](https://w3c.github.io/webcomponents/spec/imports/) 

```
<link rel="import" href="/path/to/awesome/file.html"> 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，您可以使用一些非常简单的 JavaScript 来获取 HTML 内容并将其加载到页面上:

```
const content = document.querySelector('link[rel="import"]');
const outputElement = document.querySelector('#placeToShoveContent');

outputElement.appendChild(content.import); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这将需要更多的 JavaScript 代码来以编程方式处理多个导入，但这是一个很好的干净的解决方案，使用原生特性可以很好地工作。**不幸的是**，这项功能目前正处于**的工作草案**阶段，还没有准备好投入生产使用。

查看[我可以使用的网站](https://caniuse.com/#feat=imports)(在撰写本文时)它只在最新版本的 Chrome 和 Opera 中可用。哭丧着脸。

### 使用 JavaScript 将其加载

同样，您可以使用一些普通的 JS 甚至 jQuery 来加载其他文件，比如:

```
$(function() {
    $('#placeToShoveContent').load('path/to/file.html');
}); 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，但是这意味着将 jQuery 加载到项目中，就为了这么做。普通的 JS 解决方案比理想的稍微冗长一些，主要使用 AJAX 请求文件(这也是`jQuery.load()`函数真正在幕后做的事情)。

## Webpack 和 require.context()来救援！

那么，既然我们已经在使用 Webpack 来构建这个东西，那么让我们利用 Webpack 的一个伟大特性:require.context()。

(顺便说一句，如果你正在寻找一个[好的 Webpack 初学者工具包](https://robkendal.co.uk/webpack-project-starter-kit/)，那么我在 GitHub 上有一个[很棒的模板)](https://github.com/bpk68/web-template)

### 首先，配置 html-loader 来处理我们的文件

首先，因为我们正在加载 HTML 文件，我们需要安装 Webpack 的[HTML-loader](https://webpack.js.org/loaders/html-loader/)；npm/yarn 的一个简单壮举:

`npm i -D html-loader`或`yarn add --dev html-loader`

从这里，我们需要将 html-loader 配置添加到我们的`webpack.config.js`
中

```
module: {
  rules: [
    {
      test: /\.html$/,
      exclude: /node_modules/,
      use: {
        loader: 'html-loader'
      }
     },
     ...
     {
       //other rules here
     }
   ]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们这样做，Webpack 可以识别和处理 HTML 文件:

`require('/path/to/html/file.html');`

但这只是一个文件，我们需要加载一堆文件，这将是一个不断增长的列表——一次添加一行是不切实际的。

### 使用 require.context()

现在我们可以加载 HTML 文件，我们可以开始使用`require.context()`加载文件夹中的一些文件并处理它们的内容。

以下是我为此所做的:

```
// grab the element where we'll output the HTML to
const output = document.querySelector('#output');

// create a 'cache' where we can store our built up HTML from our fragments
let htmlFragmentCache = {};

// here, we're creating an anonymous function that loads up our HTML fragments
// then it adds them to our cache object
const importAll = requireContext => requireContext.keys().forEach(key => htmlFragmentCache[key] = requireContext(key));

// next, we call our importAll() function to load the files
// notice how this is where we call the require.context() function
// it uses our file path, whether to load subdirectories and what file type to get
importAll(require.context('./fragments', false, /.html$/));

// finally, we can loop over our cache's keys and add the HTML to our output element
Object.keys(htmlFragmentCache).forEach(key => output.innerHTML += htmlFragmentCache[key]); 
```

Enter fullscreen mode Exit fullscreen mode

而且就是这么简单！当然，即使是那寥寥几行也可以浓缩成一个[匿名函数](https://robkendal.co.uk/arrow-functions-in-javascript/)(实际上，[立即调用函数表达式](https://blog.mgechev.com/2012/08/29/self-invoking-functions-in-javascript-or-immediately-invoked-function-expression/)或 IIFE 的一个例子)来创建一个更干净、更简洁的最终结果:

```
(context => {
    // need to clear out the current element's contents (just in case!)
    output.innerHTML = '';

    // now, load up the html fragments and add them to the page
    context.keys().forEach(key => output.innerHTML += context(key));
})(require.context('./fragments', false, /.html$/)); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了。一种非常干净、简单的方式，在一个文件夹中加载一堆 HTML 文件，在一个 JavaScript 文件中使用`require.context()`，使用 Webpack 加载、处理和捆绑。波什！

## 还有别的想法吗？

评论:你得到了他们！让我知道你过去会(或已经)如何处理这种事情。你有更简单的解决方法，更好的吗？我很想听听你的想法和主意。