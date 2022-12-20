# JS 生态圈为什么牛逼！

> 原文：<https://dev.to/gmartigny/why-the-js-ecosystem-is-awesome-48nl>

Javascript 世界是当今最活跃的开发生态系统。它的流行将它从一个简单的脚本工具推向了今天网络的主干。浏览器的功能日益强大，NPM 注册中心增长到了 100 万个软件包，Node 早已证明了它是一种有能力的后端语言。

今天，我们将一起从零开始创建一个小项目，并充分利用这一切。我想告诉你，你不需要多年的经验来创造一些很酷的东西。系好安全带，这是一段漫长的旅程，但我向你保证，最终你可以自己完成。

## 第一幕:代码

假设您有一个想要编码的想法。不，不，简单一点的。适合一种功能的东西。类似于为每个用户名创建一个独特的图像，返回任何时区的时间或者返回一个随机的颜色。

出于我们的目的，我们将创建一个函数，给出给定尺寸的空白图像。没什么大不了的，只是创建一个画布，给它上色，然后返回数据。我们将称之为`lorem-pix`。

所以，我以同样的方式开始每个项目:

```
$ mkdir lorem-pix
$ cd lorem-pix
$ git init
$ npm init 
```

Enter fullscreen mode Exit fullscreen mode

然后，让我们创建那个`index.js`并开始编码:

```
const loremPix = (width, height, color) => {
    const canvas = createCanvas(width, height);
    const ctx = canvas.getContext("2d");

    ctx.fillStyle = color;
    ctx.fillRect(0, 0, width, height);

    return canvas.toDataURL("image/png");
};
module.exports = loremPix; 
```

Enter fullscreen mode Exit fullscreen mode

此时，你应该注意到`createCanvas`没有被定义。如果我在浏览器中，我只需要做`document.createElement("canvas")`，但是我已经知道我不想留在浏览器环境中，Node 没有`Canvas`支持。😱
我们能做什么？

## 第二幕:NPM

拥有成千上万的软件包， [NPM](https://npmjs.com) 有可能为你的任何问题提供解决方案。由于 NPM-cli 与 Node 捆绑在一起，可以说它是 Javascript 中最有影响力的程序。

> 不要把 NPM 和 NPM 混淆了。前者是保存所有代码的公共注册中心，而后者是向注册中心请求的实用程序。

一点点研究给了我所需要的。剩下要做的就是获取所需的依赖项。

```
$ npm install canvas 
```

Enter fullscreen mode Exit fullscreen mode

并在我们的`index.js`文件的顶部添加一个`require`。

```
const { createCanvas } = require("canvas"); 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，遗憾的是，Node 目前不支持 [`import`语句](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import)。

瞧，我们的功能完成了。我们甚至可以在 NPM 出版，这样任何人都可以使用它。只要确保您的`package.json`已正确填写，我们就可以开始了。

```
$ npm publish 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这已经很棒了。我们有一个依赖于其他人代码的功能，我们发布了它，所以任何人都可以依赖我们。但是为什么不更进一步呢？

## 第三幕:CLI

自从 [NPM-cli `5.2.0`](https://github.com/npm/npm/releases/tag/v5.2.0) 之后，有了一个新的命令:`npx`。多亏了它，你可以用一个命令行执行托管在 NPM 上的代码。它神奇地下载、安装需要的东西并运行所需的二进制文件。

但是我们需要一个二进制可执行文件。我们的 JS 函数很棒，但是只能用 JS 脚本调用。
让我们创建一个名为`cli.js`的新文件来解决这个问题。在操作系统上运行纯文本文件是行不通的。我们需要告诉哪个程序必须解析其中的代码。在我们的例子中，我们需要它是 Node(默认为 unix 上的`bash`或 windows 上的`cmd.exe`)。幸运的是，我们可以用一个 [shebang](https://www.wikiwand.com/en/Shebang_(Unix)) 做到这一点。

```
#!/usr/bin/env node const loremPix = require(".");
const { writeFileSync } = require("fs");

const inputs = process.argv.slice(2);

const dataURL = loremPix(...inputs);

writeFileSync("lorem.png", dataURL.split(",")[1], "base64");
console.log("Done creating image."); 
```

Enter fullscreen mode Exit fullscreen mode

在第一行，你可以注意到 shebang 的事情。然后，我们导入自己的库和节点文件系统实用程序。
接下来，我们从命令行参数中收集输入，第一个是程序(`node`)，第二个是执行的文件(`cli.js`)。之后的任何内容都被视为函数的参数。
最后，脚本[使用返回的数据写入文件](https://nodejs.org/api/fs.html#fs_fs_writefilesync_file_data_options)。细节并不重要，但它需要一个文件名、一些数据和编码方法。

在添加了执行权限之后，我们现在可以像运行安装在您计算机上的任何程序一样运行这个脚本了。

```
$ chmod +x cli.js
$ ./cli.js 200 150 red
> Done creating image. 
```

Enter fullscreen mode Exit fullscreen mode

嘣！一个名为`lorem.png`的新文件出现在`cli.js`旁边，你猜对了，这是一个 200×150 像素的红色图像。

[![Red image](img/68fd9fc680b8545fb1f781bf87cb82c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wGjPEagk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o61xkrhzfn1zxjfp2vow.png)

> 难以置信！😮

这意味着任何安装了 Node 的人都可以调用`npx`来使用我们神奇的库。

```
$ npx lorem-pix 200 150 red 
```

Enter fullscreen mode Exit fullscreen mode

那么现在，如果我们想让用户即使没有节点也能使用它呢？

## 第四幕:API

从历史上看，JS 是为了在后端使用而创建的，Node 是那段历史的合法继承者。如果我们的代码部署在一个节点后端，它可以通过一个简单的 URL 调用来运行。
我要用 [ZEIT 的`now`](https://zeit.co/now) 因为我爱它。太简单了，很痛。

首先，我们可以再次添加一个名为`api.js`的新文件，并根据[填充文档](https://zeit.co/docs/v2/deployments/official-builders/node-js-now-node/)。

```
const loremPix = require(".");
const { parse } = require("url");

module.exports = (request, response) => {
    const { query } = parse(request.url, true);
    const { width, height, color } = query;

    const data = loremPix(width, height, color);

    response.writeHead(200, {
        "Content-Type": "image/png",
    });
    response.write(data.split(",")[1], "base64");
    response.end();
}; 
```

Enter fullscreen mode Exit fullscreen mode

几乎与`cli.js`一样:导入我们的库，解析 URL 以获得正确的参数，并将结果写回给用户的响应。

我们还需要一个`now.json`文件来配置`now`。

```
{  "version":  2,  "builds":  [  {  "src":  "api.js",  "use":  "@now/node"  }  ],  "routes":  [  {  "src":  "/api/(?<width>[^/]+)/?(?<height>[^/]*)/?(?<color>[^/]*)",  "dest":  "/api.js?width=$width&height=$height&color=$color"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

首先我们告诉`now`使用 Node 来运行`api.js`。有点像《T2》里的那种。
末尾的象形文字是正则表达式。用户懒，我也懒，像`api.js?width=200&height=150`这样的 URL 我解析起来更容易，但是用户写`api/200/150`就更简单了。regexp 就是通过重新路由请求来做到这一点的。

好了，是时候部署 API 了。

```
$ npx now login
$ npx now 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，程序会告诉你可以使用哪个 URL 来执行代码。以我为例:
[https://lorem-pix-o5fjmzam5.now.sh/api/200/150/red](https://lorem-pix-o5fjmzam5.now.sh/api/200/150/red)

每当有人请求图像时，API 就返回图像。这意味着任何 HTML 页面都可以使用它，无需任何安装。

```
<img src="https://lorem-pix-o5fjmzam5.now.sh/api/200/150/red" alt="placeholder"/> 
```

Enter fullscreen mode Exit fullscreen mode

等一下！

## 第五幕:网络服务

任何 HTML 页面都可以用？为什么不创建我们自己的网页呢？
我们已经设置了`now`来托管 JS 代码，我们可以在它旁边添加一个静态 HTML 页面。不仅仅是一个登陆页面来展示我们的项目，我们可以让用户输入结果的实时预览。
还有另一个文件，这次命名为`index.html`。

```
<h1>Lorem pix</h1>
<form id="form">
    ...
</form>
<img src="" alt="live preview" id="preview"/>
<script src="front/live-preview.js"></script>
<script>
const form = document.getElementById("form");
const img = document.getElementById("preview");

form.addEventListener("change", () => {
    const { width, height, color } = form;
    livePreview(img, width.value, height.value, color.value);
});
</script> 
```

Enter fullscreen mode Exit fullscreen mode

为了便于组织，我将 CSS 和 JS 文件放在了`front`文件夹下。这是我们的脚本的样子。

```
const API_URL = "https://lorem-pix-o5fjmzam5.now.sh/api";

const livePreview = (img, width, height, color) => {
    const url = `${API_URL}/${width}/${height}/${encodeURIComponent(color)}`;
    img.src = url;
    return url;
}; 
```

Enter fullscreen mode Exit fullscreen mode

相当简单！
我只是用了`encodeURIComponent`来避免`#`字符扰乱网址。
最后，我们需要告诉`now`提供`now.json`中的静态文件。

```
"builds":  [  ...  {  "src":  "index.html",  "use":  "@now/html-minifier"  },  {  "src":  "/front/*",  "use":  "@now/static"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

别忘了再次部署`npx now`和[我们正在直播](https://lorem-pix-8kqz0zewu.now.sh/)。

## 结论

多好的旅程啊！能够访问与包、CLI、API 和前端 webservice 相同的功能，对我来说仍然是不可思议的。我强烈鼓励每个阅读的人去尝试，去做他们自己的事。你可以在 Github 访问整个[代码库，并从这里复制/粘贴。
只要有一点创意，而不仅仅是制作一张空白图片，我相信你们所有人都能提供一些很棒的产品。请务必在下面的评论中分享它们。](https://github.com/GMartigny/lorem-pix)

和平🙏