# npm 创建包 JSon 创建您的第一个 npm 包

> 原文：<https://dev.to/therealdanvega/creating-your-first-npm-package-2ehf>

这个周末，我开始着手我的第一个 npm 包。我不敢相信我写代码已经有多久了，我从来没有费心去创建我自己的 npm 包，但是现在我们在这里。我用 Gridsome 和 markdown 建立了我的最新网站，你可以在这里阅读所有相关内容。在 markdown 文件中，我想要一种简单的方法来插入 twitter 状态并嵌入推文。

我会在以后的博客中告诉你更多关于 Gridsome 插件的内容，但是现在，我想向你展示如何创建你的第一个 npm 包。我在做这个的时候学到了一些东西，我想和你们分享一下。

## 先决条件

我假设你至少知道 node & npm 是什么，并且以前写过 JavaScript。如果你不知道这两者中的任何一个，并且想要我写一些关于开始使用它们的东西，请让我知道。

在我们开始编写代码之前，您还需要一些东西。

*   [Visual Studio 代码](https://code.visualstudio.com/)或者你喜欢的编辑器
*   [节点& NPM](https://nodejs.org/en/)
*   [NPM 账户](https://www.npmjs.com/)

## 创建您的 npm 包

您要做的第一件事是创建一个新文件夹来存放您的 npm 包。对于这个例子，我将创建一个名为 **wrap-with-poo** 的新目录。是的，你没看错。

进入该文件夹并键入以下内容:

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

这将问你一堆问题，然后创建一个 package.json。如果你还不知道某些问题的答案，不要担心，你可以稍后回来回答它们。

```
This utility will walk you through creating a package.json file.
It only covers the most common items and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterward to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (wrap-with-poop)
version: (0.1.0) 0.1.0
description: This package will take any string you give it and wrap it with the poop emjoi
entry point: (index.js)
test command:
git repository:
keywords: node,npm
author: Dan Vega
license: (ISC) MIT
About to write to /Users/vega/dev/npm/wrap-with-poop/package.json:

{
  "name": "wrap-with-poop",
  "version": "0.1.0",
  "description": "This package will take any string you give it and wrap it with the poop emjoi",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "node",
    "npm"
  ],
  "author": "Dan Vega",
  "license": "MIT"
}

Is this OK? (yes) yes 
```

Enter fullscreen mode Exit fullscreen mode

## 编写你的插件

接下来，在 Visual Studio 代码中打开这个项目，并创建 index.js。创建这个文件的原因是，在 package.json 中，您说这是您的入口点。在 index.js 中添加以下代码:

```
module.exports = (str) => {
    return `💩${str}💩`;
} 
```

Enter fullscreen mode Exit fullscreen mode

module.exports 对象允许我们组织一些相关的代码，然后将其作为模块公开。这意味着当我们完成时，我们可以将这个模块导入到另一个应用程序中。在本例中，我们分配了一个 arrow 函数，这意味着我们公开了一个函数，它接受一个名为 str 的参数，并返回用 poo 表情符号包装的字符串。这就是你在这个项目中需要做的一切。这是一个非常简单的包，但它将有助于完成一些事情。

## npm 地方发展

现在您已经准备好我们的包，您需要在另一个项目中测试它。在现实世界中，您应该针对它编写一些单元测试，但是我想把它留给另一篇文章& screencast。

接下来，创建一个名为 wrap-with-poo-testing 的新目录(在您的包之外)。您将再次需要运行 npm init，但是这一次您可以添加-y 参数来跳过所有问题，这次它们不太重要。

```
npm init -y

Wrote to /Users/vega/dev/npm/wrap-with-poo/package.json:

{
  "name": "wrap-with-poop",
  "version": "0.1.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
} 
```

Enter fullscreen mode Exit fullscreen mode

### NPM 安装

在这个项目中，创建一个名为 app.js 的新文件。这通常是您通过运行以下命令来安装所需的 npm 软件包的位置。

```
npm install wrap-with-poo 
```

Enter fullscreen mode Exit fullscreen mode

问题是你还没有发布你的新插件，所以它不在 npm 中。在开发包时，您需要一种在本地引用包的方法。您可以使用软件包的绝对路径运行 npm install。

```
npm install /Users/vega/dev/npm/wrap-with-poo 
```

Enter fullscreen mode Exit fullscreen mode

这将更新您的 package.json，看起来像这样

```
{
  "name": "npm",
  "version": "0.1.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "wrap-with-poo": "file:../wrap-with-poo"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要在您的包中测试[预安装/后安装挂钩](https://docs.npmjs.com/misc/scripts)，那么您将会想要使用这种方法。如果你不在乎在当地开发 NPM 项目的最佳方式是使用 [npm 链接](https://docs.npmjs.com/cli/link.html)。

### NPM 链接

npm 链接是一个允许您在项目和依赖项之间创建符号链接的过程。首先，您需要进入 wrap-with-poo 目录并运行以下命令。

```
npm link 
```

Enter fullscreen mode Exit fullscreen mode

这将获取您的包，并在 npm 全局文件夹中创建一个指向它的符号链接。

**/用户/织女星/。nvm/versions/node/v 10 . 15 . 0/lib/node _ modules/wrap-with-poo->/Users/Vega/dev/NPM/wrap-with-poo**

这意味着只需一个简单的步骤，您的项目就可以在任何项目中使用。您需要做的下一件事是进入项目 wrap-with-poo-testing 并运行下面的命令。

```
npm link wrap-with-poo 
```

Enter fullscreen mode Exit fullscreen mode

这将输出以下内容:**/Users/Vega/dev/NPM/wrap-with-poo-testing/node _ modules/wrap-with-poo->/Users/Vega/。nvm/versions/node/v 10 . 15 . 0/lib/node _ modules/wra
p-with-poo->/Users/Vega/dev/NPM/wrap-with-poo**

这就是全部内容，不需要安装依赖项。你已经准备好开始写一些代码来玩你的新插件了。打开 app.js 并添加以下代码。

```
const poo = require('wrap-with-poo');
const boring = 'This is a boring string';
const fun = poo(boring);

console.log(fun); 
```

Enter fullscreen mode Exit fullscreen mode

并从集成终端
运行以下命令

```
node app.js 
```

Enter fullscreen mode Exit fullscreen mode

您将获得以下输出

```
💩This is a boring string💩 
```

Enter fullscreen mode Exit fullscreen mode

## 发布源代码

现在我们知道我们的项目是时候公开给每个人使用了。如果你还没有这样做，我会把你的项目添加到 Github 或者任何你喜欢的源代码主机。

```
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/cfaddict/wrap-with-poo.git
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

现在它已经在 Github 上了，返回并在 package.json 中添加一个条目，这样每个人都知道在哪里可以使用 homepage 键找到源代码。

```
{
  "name": "wrap-with-poo",
  "version": "0.1.0",
  "description": "This package will wrap any string you give it with the poop emoji",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "node",
    "npm",
    "poop"
  ],
  "author": "Dan Vega",
  "license": "MIT",
  "homepage": "https://github.com/cfaddict/wrap-with-poo"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 出版 NPM 包

现在是时候将我们的项目发布到 npm，以便任何人都可以使用它。如果这是你第一次发布一个包，在 wrap-with-poo 目录下打开一个终端，输入下面的命令。

```
npm adduser 
```

Enter fullscreen mode Exit fullscreen mode

这将要求您提供 npm 帐户信息，如用户名、密码和电子邮件。

```
vega wrap-with-poo (master) $ npm adduser
Username: therealdanvega
Password:
Email: (this IS public) danvega@gmail.com
Logged in as therealdanvega on https://registry.npmjs.org/. 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以发表文章了，但是有几件事你需要记住。首先，每个 npm 包必须有一个唯一的名称。我会前往 [npm](https://www.npmjs.com/) 快速搜索你的包裹。我已经发布了用便便包裹的包，所以你的包需要一个新的唯一的名字。

接下来你需要知道的是你的版本号很重要。我从 0.0.1 开始，然后一步步向上。一旦发布了特定版本，就不能再次发布相同的版本。将许多特性构建到一个版本中，然后发布它，这是一个好主意。如果你运行

```
npm version 
```

Enter fullscreen mode Exit fullscreen mode

它会告诉你你当前的版本是什么。

```
{ 'wrap-with-poo': '0.1.0',
  npm: '6.7.0',
  ares: '1.15.0',
  cldr: '33.1',
  http_parser: '2.8.0',
  icu: '62.1',
  modules: '64',
  napi: '3',
  nghttp2: '1.34.0',
  node: '10.15.0',
  openssl: '1.1.0j',
  tz: '2018e',
  unicode: '11.0',
  uv: '1.23.2',
  v8: '6.8.275.32-node.45',
  zlib: '1.2.11' } 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，你可以通过运行
来发布你的新项目

```
npm publish 
```

Enter fullscreen mode Exit fullscreen mode

这可能需要几秒钟，但如果一切顺利，你的包裹现在应该在 npm 上[直播。](https://www.npmjs.com/settings/therealdanvega/packages)

恭喜你发布了你的第一个 npm 包！！！

现在，您可以进入任何已经有 package.json 的项目，并键入以下内容

```
npm install wrap-with-poo 
```

Enter fullscreen mode Exit fullscreen mode

就像我们在上面的测试例子中那样使用它。

## 文档

我知道有些人说你应该从一开始就创建文档，但我并不总是确定我的代码最终会是什么样子，所以我通常会等待。在项目的根目录下创建一个 README.md，并添加一些关于项目的信息。

*   你的 npm 包是做什么的？
*   你为什么要创造它。
*   你是怎么安装的？
*   有什么配置选项吗？

## 结论

正如我在本文开头所说的，我不敢相信我在这个周末发布了我的第一个 npm 包。我只是从来没有真正需要这样做，直到现在，但我真的很兴奋地了解到这是多么容易。如果这是你的第一个 npm 包，请给我留下一些评论，或者在你的包上线时给我发推特！

编码快乐！
丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*