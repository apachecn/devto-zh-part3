# 如何在 NPM 上发布包

> 原文：<https://dev.to/jumpalottahigh/how-to-publish-a-package-on-npm-816>

几周前，我在 NPM 上创建并发布了我的第一个节点包，非常有趣。令我惊讶的是，这比我想象的要容易得多。

至少有一些[样板文件](https://github.com/sindresorhus/node-module-boilerplate)和[搭建工具](https://github.com/kentcdodds/generator-kcd-oss)可以帮助你开始，虽然它们可能是正确的方法，因为它们带有所有的工具、测试和挂钩，这里有一个实际发布节点包的简单例子。有很多关于如何做的指南，包括 npm 文档上的[这篇文章](https://docs.npmjs.com/creating-node-js-modules)，但是这里有一个关于如何进行最小设置的方法。

### 🔁在 GitHub 上创建一个新的资源库

创建一个新的存储库，并在本地机器上克隆它。然后把目录换到里面，用你最喜欢的编辑器打开文件夹。

```
git clone https://github.com/jumpalottahigh/how-to-publish-to-npm
cd how-to-publish-to-npm/
code . 
```

Enter fullscreen mode Exit fullscreen mode

### 运行 npm 初始化

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

你也可以传递`-y`来自动回答初始化器中的每个问题。我会说你可能想回顾一下信息，所以可以慢一点。因为我们只是想看看事情是如何工作的，缺省值就足够了。

检查您的`package.json`键，确保`main`键指向模块的入口点。现在，让我们把它指向`index.js`。

### 📦编写模块

创建 index.js，

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

…让我们导出一个名为`sum`的简单函数，它将两个数相加并返回结果。

```
exports.sum = function(a, b) {
  return a + b
} 
```

Enter fullscreen mode Exit fullscreen mode

### 📢发布包

将您的所有更改暂存、提交并推送到 GitHub 上的远程存储库。

```
git add .
git commit -m "Export a sum function"
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要登录 npm，因此运行:

```
npm login 
```

Enter fullscreen mode Exit fullscreen mode

在您完成这个流程之后，您就可以使用:
发布您的包了

```
npm publish 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，确实那么容易！前往[npmjs.com](https://www.npmjs.com/)，用你给它起的名字搜索你新发布的包。

### 🍴消费模块

最后但同样重要的是，让我们试一试。在你最喜欢的项目中安装软件包:

```
npm i how-to-publish-to-npm 
```

Enter fullscreen mode Exit fullscreen mode

然后在代码中使用它:

```
// Importing the module using a named import
import { sum } from 'how-to-publish-to-npm'

console.log(sum(7, 11)) // 18 
```

Enter fullscreen mode Exit fullscreen mode

### 🆙升级软件包

假设你对你的函数做了一些修改，想要发布一个新的版本。可以用`npm version`来撞包版本。

```
npm version major # 1.0.0
npm version minor # 0.1.0
npm version patch # 0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

阅读更多关于[撞包版本](https://docs.npmjs.com/cli/version.html)的内容。

然后你可以运行`npm publish`，用新的版本再次发布这个包。

### 🌯包裹

就这些了！祝贺🎉！这真的没有人们想象的那么复杂(或者至少我是这样认为的)。我希望这能激励您创作自己的模块，并与 Node 社区共享它们。

另外，请不要将这样的模块用于生产。为了创建一个概念验证(PoC)并得到一些东西，我们严重忽视了测试和持续集成。没有什么可以阻止你从小处着手，在你需要的时候引入所有的功能，或者从一个生成的项目开始。你做你的！