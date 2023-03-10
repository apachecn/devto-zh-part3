# 我如何为一切配置 VSCode

> 原文：<https://dev.to/amanhimself/how-i-configure-vscode-for-everything-23c9>

[![cover-img](img/944ed64f8cb83fb82bf2a49681269717.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2PHSu2GS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2Aj-b1dZsAJGfhUEQVHu2Wpw.jpeg)

最近，我在乌克兰的 Boryspil 机场写博客文章，突然我的 VSCode 停止工作了。它真的坠毁了！不是一次，而是两次不到 30 分钟。有些内容是自动保存的，有些则永远消失了…

我起初很沮丧，为什么它必须在那一刻崩溃！接下来，重新打开编辑器后，我生自己的气，因为我不得不重新写一些没有保存的东西。

[![ss1](img/75da0e40bc79809544aa832dc7443ba3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cuPpFfQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://memegenerator.net/img/instances/65662553/vscode-you-were-the-chosen-one.jpg)

此外，在这次经历之前，有时确实感觉到我有一些扩展，但我并没有使用，因为我在最近几个月停止了一些框架的工作(例如，*Angular*)。

我做了什么？在所有情绪都无济于事后，我不得不想办法更好地应对。在一个不是很糟糕的网络上，但由于 WIFI 限制，我不得不每 30 分钟登录一次，我重新安装了 [VSCode](https://code.visualstudio.com/) 。

VSCode 使用 Electron 作为其基础，这使得它能够跨平台并在 macOS、Windows 和 Linux 上工作。它是使用 Node.js 构建的，完全支持任何 JavaScript 开发人员。这就是我所说的双赢。与我以前用过的编辑器( *Atom* )和 IDE ( *Webstorm* )相比，它要快得多。

## 主题

我做的第一件事是安装我整天都习惯看到的主题。我喜欢我的编辑有魅力，有吸引力。因此，我出于不同的目的使用以下主题。

*   大多数时候，因为我喜欢紫色的背景！)
*   吸血鬼官方(我在 iTerm 设置中经常使用这个)
*   夜猫子(尝试新事物)
*   材料-图标-主题(用于文件图标)

[![ss2](img/eb93a923adc23eb7331fcf61029a428e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oBQgm4k4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/D4LuaCtUcAE6rGP%3Fformat%3Djpg%26name%3Dlarge)

## 配置 VSCode

接下来，我改变了一些我能从之前的设置中记住的东西。

*   将制表符大小设置为两个空格！(*我们还可以做朋友*
*   为 HTML 和 JavaScript 启用 emmet
*   延时 5 秒使能`autoSave`
*   使用操作系统的终端设置和 shell ( *zsh* )
*   在保存和粘贴来自其他地方的代码片段时启用格式化文件👀
*   禁用小地图，我不喜欢给不必要的空间，加上这些天我们正在编写模块化的功能模块，不是吗？
*   此外，启用`wordwrap`

```
"editor.tabSize":  2,  "emmet.includeLanguages":  {  "html":  "html",  "javascript":  "javascriptreact"  },  "workbench.iconTheme":  "material-icon-theme",  "files.autoSave":  "afterDelay",  "files.autoSaveDelay":  5000,  "terminal.external.osxExec":  "iTerm.app",  "terminal.integrated.shell.osx":  "zsh",  "editor.wordWrap":  "on",  "editor.formatOnSave":  true,  "editor.formatOnPaste":  true,  "editor.minimap.enabled":  false 
```

Enter fullscreen mode Exit fullscreen mode

## 我使用的扩展

对我来说，下一步是让这个编辑器在一些令人惊叹的维护和免费扩展的帮助下工作起来。

*   :表情符号:
*   括号对着色机
*   代码拼写检查器(我用 VSCode 写了很多博文)
*   Code:: Stats(一个小的统计工具，为了好玩)
*   数据预览(当我使用 VSCode 时，我喜欢它打开每一个文件，无论是带有图表的 excel 表格还是简单的 JSON 配置。这个扩展允许我这样做)
*   埃斯林特
*   缩进 4 到 2(将缩进从制表符 4 个空格转换为 2 个空格。我们还是朋友吗？)
*   expressjs(我写的 snippets 包)
*   markdownlint(我有时候一天写的 Markdown 比 JavaScript 代码还多)
*   GitHub 拉请求
*   GraphQL ( *由 Prisma* 出版)
*   进口成本
*   npm 智能感知
*   路径智能感知
*   pug(我为 JADE 和 Pug 模板语言编写的另一个片段，用于一些旧项目)
*   CSS 类名的智能感知
*   反应原生工具
*   整棵树
*   版本镜头
*   设置同步(我在这里提到的最重要的扩展之一)
*   字数
*   更漂亮的代码格式化程序(不能没有它，句号。)

对于更漂亮，我喜欢使用我自己的一套配置，如下所述。

```
"prettier.jsxSingleQuote":  true,  "prettier.printWidth":  100,  "prettier.semi":  false,  "prettier.useTabs":  true,  "prettier.tabWidth":  2, 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这就是我现在为我的 JavaScript、NodeJS、React 和 React 本机工作使用的设置。我希望你喜欢阅读这篇文章。

**编码快乐！**

你呢？您使用什么 VSCode 配置？它看起来怎么样？有我不知道的与降价相关的秘密配置吗😁，请告诉我！

## 进一步阅读

✨ [我如何设置我的 M1 Macbook Pro](https://dev.to/amanhimself/setup-macbook-m1-for-web-and-react-native-development-8la)

* * *

我经常写关于网络技术的文章，并且反应自然。你可以在 **[Twitter](https://www.twitter.com/amanhimself)** 上关注我，或者你可以 **[订阅我的每周简讯](https://amanhimself.substack.com/)** 直接在你的收件箱里接收我所有的教程📧