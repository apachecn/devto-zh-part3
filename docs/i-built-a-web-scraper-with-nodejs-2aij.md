# 我用 NodeJS 构建了一个 web scraper

> 原文：<https://dev.to/tomwritescode/i-built-a-web-scraper-with-nodejs-2aij>

本文最初发表于我的个人博客- [tomWritesCode](https://tomwritescode.com/raspberrycandy-web-scraper-with-nodejs)

### 这篇文章讲述了我如何制作一个终端命令，它进入 rasoberryCandy 的 VS 代码市场页面，搜索它的下载次数，然后使用 Figlet 和 Cheerio 以一种有风格的方式返回它。

[![Large GIF 912x276](img/c77eb1bf2a3dbb19b6ca6265dece0318.png)](//images.ctfassets.net/u434ytvnd3bo/5XQ2c9F4uB37GT0jnBWrZP/79b58c210c0d4268a47e6af7f4ac4bce/Large_GIF_912x276.gif)

浏览几个教程，展示如何从我的终端制作一个节点应用程序，这将向我显示我的下载次数，并计算 raspberryCandy 发布了多长时间。

我浏览了 scotch.io 的这篇文章，其中使用了 NodeJS、Cheerio 和请求-承诺。

* * *

**-请求-承诺-** *发出支持承诺的 HTTP 请求。标准请求承诺的扩展。*

**- Cheerio -** *帮助遍历 DOM，让我们选择我们想要提取的页面部分。*

* * *

Figlet 是一个很棒的从文本制作 ASCII 艺术的包，它有一个不同方法的库。粉笔用于给终端中的响应文本着色，这意味着我可以保持返回的 raspberryCandy 颜色。

我的代码的第一部分是制作一段代码，显示 raspberryCandy 何时发布以及发布了多少天，它位于命令行结果中下载次数的下方。为了做到这一点，我使用了 Javascript 内置的日期函数，使其中一个日期成为我已经知道并且不打算更改的发布日期，只需直接放入即可。另一个日期是函数被调用的日期和时间。

* * *

```
const release = new Date("February 19, 2019 11:46:11");
const current = new Date(); 
```

* * *

接下来我做了一个函数来计算 raspberryCandy 离开了多少天。该函数分两步工作，首先，它从当前日期中减去发布日期，然后给出两个日期之间的时间，单位是毫秒(这不是最可读的)。

* * *

```
function dateDiff() {
       let difference = current - release;
 } 
```

* * *

该函数的第二步以毫秒为单位获取结果，然后除以将毫秒转换为天的数学运算。这包含在一个 Math.round()函数中，以不含小数的形式返回一整天。

* * *

```
function dateDiff() {
    let difference = current - release;
    return Math.round(difference / (60 * 60 * 24 * 1000)); 
```

}

* * *

现在是主要的聚会技巧，抓取 raspberryCandy 的市场页面以获得下载数量。这是我们使用请求-承诺包的地方，并给它一个目标 url，在这个例子中是 Visual Studio Marketplace 上的页面。

在构建之前检查页面，我发现我要找的部分是一个类名称为“downloads-text”的 span。使用 Cheerio，我可以用 HTML 文档中正确的类名来定位 span，然后以纯文本的形式返回它。下面我列出了它的基本结构。

* * *

```
request-promise(URL).then(function(html) 
  {
    cheerio("span.downloads-text", html).text()
  } 
```

* * *

现在，我们已经摆脱了结构发挥作用的方式。在我的例子中，我用粉笔包包装了每个 console.log()返回，允许我设置颜色，在这个例子中，我使用 raspberryCandy 的紫色和浅绿色。

* * *

```
console.log(
  chalk.hex("#e592faff").bold(" WOW! raspberryCandy has:")
); 
```

* * *

另一个额外的部分是使用 Figlet，它给了我 ASCII 艺术，这围绕 Cherrio 函数，并接受使用什么字体的属性，以及水平和垂直布局。

* * *

```
console.log(
  chalk.hex("#00feff")(
    figlet.textSync($("span.downloads-text", html).text(), {
      font: "Big",
      horizontalLayout: "default",
      verticalLayout: "default"
    })
  )
); 
```

* * *

现在，所有这些都拼凑在一起了，剩下要做的最后一件事就是将它作为一个命令链接到我的终端，而不是必须转到文件系统并运行 JS 文件。为此，我们在文件的顶部添加一行，这将允许我们将命令添加到 package.json。

* * *

```
#!/usr/bin/env node 
```

* * *

这一行将让它作为一个节点命令被触发并运行。这也将使我们添加到 package.json 的下一部分工作。在 package.json 中，我添加了:

* * *

```
"bin": {
  "raspberry": "./raspberryScraper.js"
}, 
```

* * *

最后一步是在终端运行文件夹中的 ***npm 链接*** 。这采用 bin 命令，在本例中为 ***【覆盆子】*** ，然后启动命令 ***。/raspberryScraper.js"*** 。这类似于任何类似于 ***npm run start*** 或***Gatsby develop***的简写命令。

* * *

## 链接

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [汤姆 writes code](https://github.com/tomWritesCode)/[raspberryScraper](https://github.com/tomWritesCode/raspberryScraper)

### NodeJS web scraper 显示我的 raspberryCandy VS 代码主题在终端中有多少下载。

<article class="markdown-body entry-content p-5" itemprop="text">

# 覆盆子刮刀

### NodeJS web scraper 显示我的 raspberryCandy VS 代码主题在终端中有多少下载。

* * *

[![raspberryScraper](img/4fbac805e31ef0e5cd8c0f3a65d2bf26.png)](https://camo.githubusercontent.com/da81b84243983e4668e353aa7d186a7306e82b68/68747470733a2f2f7265732e636c6f7564696e6172792e636f6d2f746f6d777269746573636f64652f696d6167652f75706c6f61642f76313535373832323337302f4c617267655f4749465f393132783237362e676966)

* * *

在构建了我的 VS 代码主题 raspberryCandy 之后，我想要一个更简单的方法来检查我有多少下载，同时也很好奇如何使用 NodeJS 作为一个网页抓取器。

它使用 Cherrio 和 request-promise 以及 Figlet 和粉笔来设计终端。

</article>

[View on GitHub](https://github.com/tomWritesCode/raspberryScraper)

### -[-**scotch . io 文章大部分资源我都是从那里得到的。**](https://scotch.io/tutorials/scraping-the-web-with-node-js)

### -[-**请求-承诺 GitHub 页面**-](https://github.com/request/request-promise)

### -[-**Cheerio GitHub 页面**-](https://github.com/cheeriojs/cheerio)

### - [**女儿 NPM 页**](https://www.npmjs.com/package/figlet)