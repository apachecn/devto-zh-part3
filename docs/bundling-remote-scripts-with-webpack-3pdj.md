# 用 Webpack 捆绑远程脚本

> 原文：<https://dev.to/pimterry/bundling-remote-scripts-with-webpack-3pdj>

作为如今的 JavaScript 开发者，你使用的几乎所有东西都来自 npm。不幸的是，并不完全是这样:仍然有一小部分脚本期望从某个远程 CDN 中包含进来，当捆绑您的应用程序时，这些会带来问题。

您可以根据需要使用 CDN 中的这些脚本。如果你这样做，你将会失去捆绑收益的机会，比如树摇动，但是更重要的是，你现在必须在你的其他捆绑包的同时从一个以上的域中独立地加载脚本。这意味着另一个失败点，意味着您需要在主应用程序中使用逻辑来等待，直到远程脚本加载完毕，然后再使用它，并且还可能处理加载失败。

相反，您可以直接下载脚本，保存到您的代码库(“供应商”它)，并像对待自己的源代码一样对待它。如果它改变了呢？这些 CDN 脚本中的许多经常改变，所以你需要重复地更新它，每一次改变都是你的代码库和 git 历史中额外的噪音和混乱。

我最近在研究 HTTP 工具包时遇到了这个问题，试图将 JS SDK 用于第三方服务，该服务只能从 CDN 获得，并且不在 npm 上发布。幸运的是，还有另一个选择:webpack 可以为我们解决这个问题。

## Val 加载器

Webpack 鲜为人知的 [val loader](https://github.com/webpack-contrib/val-loader) 允许你轻松定义自己的加载逻辑，在构建时运行。当您使用大多数 webpack 加载器加载文件时，它们会读取文件，以某种方式转换内容，并向您的包中添加一些内容，这些内容稍后会从初始的 import/require 语句中返回。

当您使用 val loader 加载文件时，它会:

*   将文件内容作为节点模块执行
*   从模块中查找导出的函数或承诺
*   等待承诺/调用函数(该函数可能会返回一个承诺)
*   从最终结果中获取`code`属性，并将其用作原始导入/请求要捆绑和返回的内容

这意味着您可以编写一个简单的动态生成内容的节点脚本，您可以在其他地方需要该脚本，webpack 将在构建时为您预生成内容，完全自动。神奇！

## 抓取远程脚本

你大概可以看到这是怎么回事。将这些放在一起:我们需要编写一个模块，在构建时获取我们的远程脚本，并将其返回给 val loader。

实际上，这看起来像这样:

*   安装 val 加载程序:`npm install --save-dev val-loader`
*   创建一个`fetch-script.js`加载程序脚本:

```
// I'm using fetch here, but any HTTP library will do.
const fetch = require('node-fetch');

const SCRIPT_URL = 'https://cdn.example.com/your-script.js';

module.exports = function () {
    return fetch(SCRIPT_URL)
    .then((response) => {
        if (!response.ok) {
            throw new Error('Could not download ' + SCRIPT_URL);
        }
        return response.text();
    })
    .then((remoteScript) => ({ code: remoteScript }));
} 
```

*   在代码库的其余部分，像任何其他模块一样需要该模块，但使用 val loader:

```
const scriptExport = import('val-loader!./fetch-script'); 
```

就是这样！没有额外的配置，只是一个微小的节点脚本。

这样，任何需要远程脚本的代码都可以通过 val loader 导入我们的模块，并获得远程脚本，就像它是一个普通的依赖一样。它与你的应用程序的其余部分适当地捆绑在一起，并且总是立即可用，就像任何其他捆绑的脚本一样。同时，它仍然保持自动更新:每次构建，我们从 CDN 下载最新版本。您不需要将脚本提交到自己的 repo 中，或者手动检查更新。

这里需要注意的一点是:加载器脚本在运行之前不会由 webpack 构建。这意味着它需要按节点本机运行，所以没有 TypeScript/babel/等。这是一个非常简单的脚本，这是节点而不是浏览器，所以你可以使用现代的 JS。

## 接受变化

当然，根据脚本的不同，*安全地*引入变更本身是另一篇文章。一般来说，像这样的大多数远程脚本都有某种兼容性保证(否则远程使用它们是不可能的)，但是您可能仍然需要某种锁定机制。

如果远程 URL 中有可用的版本控制，这是微不足道的，如果没有，您将需要手动检查更改。

一种合理的方法是在加载器脚本中包含&检查远程文件的散列，如果它发生变化，就使构建失败，或者可能只是给自己发送一个通知。构建失败会迫使您在远程脚本更改时手动确认更改，然后更新散列，这至少可以确保您不会在应用程序中看到不可预测的更改。您需要尝试，但是这里有许多选项，这取决于您希望如何灵活地处理新的变化。

## 把所有的东西放在一起

尽情享受吧！如果你想看一个工作示例，看看 HTTP Toolkit 的 UI 如何加载 paddle.js。查看[paddle . js 加载脚本](https://github.com/httptoolkit/httptoolkit-ui/blob/master/src/model/account/paddle.js)，以及导入它的[代码](https://github.com/httptoolkit/httptoolkit-ui/blob/1aa71b9/src/model/account/subscriptions.ts#L3)。

对此有什么想法或主意吗？只是爱/恨 webpack？在下面这里评论，让我在 twitter 上知道[，或者在 reddit](https://twitter.com/httptoolkit) 上加入讨论[。](https://www.reddit.com/r/javascript/comments/ao51z2/bundling_remote_scripts_with_webpack)

*最初发布于[http toolkit . tech](https://httptoolkit.tech/blog/bundling-remote-scripts-with-webpack/)T3】*