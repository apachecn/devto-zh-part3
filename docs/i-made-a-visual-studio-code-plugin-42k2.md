# 我做了一个 Visual Studio 代码插件

> 原文：<https://dev.to/antonholmberg/i-made-a-visual-studio-code-plugin-42k2>

最近发现 [remove.bg](//remove.bg) 。这是一个非常酷的项目，可以让你(顾名思义)从图像中移除背景。所以我决定为 vscode 做一个插件，允许我右击一张图片并从图片中移除背景。

最近我一直在 vscode 和 vim 之间来回切换，但是让我对 vscode 感兴趣的一件事是，插件是用 JavaScript 编写的。

我喜欢 JavaScript 以及整个 web 堆栈的主要原因是，它可以在任何地方运行。即使你想写一个 Sketch 插件、一个移动应用、一个后端、一个 CLI 或一个桌面应用，JavaScript 知识仍然是相关的。

关于 JavaScript 已经说得够多了，让我们开始吧！

### 首发出场

首先，我需要开始一个项目。为此，我遵循了官方文档。新项目的脚手架可以用[约曼](https://www.yeoman.io/)来做。 [Yeoman](https://www.yeoman.io/) 是我之前看过的一个项目，所以我很惊喜地发现这是微软决定用于搭建的工具。

从这里我决定做一些研究。我知道 [remove.bg](https://www.remove.bg/) 有一个我可以使用的 HTTP API，但它变得更好了！已经有一个 [npm 包](https://www.npmjs.com/package/remove.bg)为我处理了这一切。

### 运行分机

因此，一旦项目搭建完成，我必须弄清楚如何运行它；这是迄今为止最容易的一步。整个过程非常简单，只需转到 debug 选项卡，然后按 run 即可。一个新的 vscode 窗口打开了，在那个窗口中，我的插件已经安装好并可以使用了。

### 在 Visual Studio 代码中包装 NPM 包

我从安装 NPM 软件包开始。这并不比为常规节点或 web 项目安装依赖项更难。

```
npm install remove.bg 
```

因此，首先，我知道 API 需要一个 API 键。因此，如果可以在 vscode 设置中指定 API 键，那就太好了。通过将这些行添加到 *package.json* 文件中，名为 *Api 键*的设置被添加到 vscode 中的设置菜单中(在名为 *Remove-bg* 的部分下):

```
{  "contributes":  {  "configuration":  [  {  "title":  "Remove.bg configuration",  "properties":  {  "remove-bg.apiKey":  {  "type":  "string",  "description":  "The API key for remove.bg"  }  }  }  ]  }  } 
```

下一步是在右键单击文件时显示菜单。不幸的是，这没有被很好地记录下来，但是一些尝试和错误引导我找到了这个解决方案:

```
 "contributes":  {  ...  "menus":  {  "explorer/context":  [  {  "command":  "remove-bg.removeBg",  "when":  "resourceExtname =~ /\\.png$|\\.jpg$|\\.jpeg$/",  "group":  "navigation"  }  ]  },  "commands":  [  {  "command":  "remove-bg.removeBg",  "title":  "Remove background"  }  ]  }, 
```

好了，这里有很多东西要学，所以让我们从*命令*部分开始。命令部分告诉 vscode，这个扩展将提供一个名为 *remove-bg.removeBg* 的命令；我们将进入该命令的实现部分，所以请耐心等待。

接下来的部分是*菜单*部分。这里我们定义了我们想要添加一个项目到下拉菜单中，当用户在浏览器标签中点击某个项目时，这个下拉菜单就会显示出来。我们提供了一个条件，即只有当用户单击文件扩展名与正则表达式匹配的内容时，它才应该显示:

```
/\.png$|\.jpg$|\.jpeg$/; 
```

所以基本上任何被 [remove.bg](https://www.remove.bg) 支持的图片。我们还指定该菜单项应该触发我们在*命令*部分指定的命令。

### 让我们写一些 JavaScript 吧！

所以当我说 vscode 插件是用 JavaScript 制作的时候，我没有撒谎——然而我确实决定用 [Typescript](https://www.typescriptlang.org/) 来做这个，它也是通常用来制作 vscode 插件的语言。

在脚手架项目的 src 文件夹中有一个名为 *extension.ts* 的文件。这是加载扩展后将运行的文件。起初我知道我必须，以某种方式，获取用户希望在设置中指定的 API 键

```
function loadApiKey(): string | undefined {
  const config = vscode.workspace.getConfiguration('remove-bg');
  return config.get('apiKey');
} 
```

上面的函数试图从设置中获取*API key*；如果未指定该设置，则返回 undefined。

好的，接下来我决定我不想在背景移除后覆盖文件，而是创建一个新文件。我决定在输出文件中添加后缀 *-no-bg* ，为了处理这个问题，我添加了一个实用函数。如果我想的话，这将允许我快速改变到一些其他的后缀/扩展名；函数看起来是这样的:

```
interface SuffixConfig {
  path: string;
  suffix: string;
  extension: string;
}

export function addSuffix({ path, suffix, extension }: SuffixConfig): string {
  const dots = path.split('.');
  return `${dots.slice(0, -1).join('.')}${suffix}.${extension}`;
} 
```

这里不可靠的部分是零和负一之间的部分。这样做的目的是返回一个新数组，其中原始数组中的最后一项被删除。在这种情况下，最后一个点后面的内容将被删除。

所以现在我已经有了最初的构建模块，我可以用官方文档和 T2 NPM 包把它们拼凑起来。

这是 *extensions.ts* 文件中的最后一段代码:

```
// this method is called when your extension is activated
// your extension is activated the very first time the command is executed
export function activate(context: vscode.ExtensionContext) {
  const disposable = vscode.commands.registerCommand(
    'remove-bg.removeBg',
    async (uri: vscode.Uri) => {
      const apiKey = loadApiKey();
      if (!apiKey) {
        vscode.window.showErrorMessage('No API key was set.');
        return;
      }

      const sourceFile = uri.path;
      const outFile = addSuffix({
        path: sourceFile,
        suffix: '-no-bg',
        extension: 'png',
      });

      try {
        await removeBackgroundFromImageFile({
          path: sourceFile,
          apiKey: apiKey,
          outputFile: outFile,
          size: 'regular',
          type: 'auto',
        });

        vscode.window.showInformationMessage('Background remove successfully!');
      } catch (e) {
        vscode.window.showErrorMessage('Failed to remove background.');
      }
    },
  );

  context.subscriptions.push(disposable);
} 
```

### 关闭思绪

插件可以在[这里](https://marketplace.visualstudio.com/items?itemName=antonholmberg.remove-bg)找到，插件的代码也可以在[这里](https://github.com/antonholmberg/vscode-remove-bg)找到。我实际上决定给 [remove.bg](https://www.remove.bg/) 的创作者写一封电子邮件，问他们我是否可以使用他们的标志。他们对此完全没有意见，实际上反应非常快。

这整个事情是一次很棒的学习经历，我经常为 vscode 插件提出新的想法——如果一天有更多的时间就好了。

我知道这篇文章可能不是有史以来最有教育意义的东西，但希望有人受到启发，为他们经常使用的工具制作一个插件。很多时候，像包装别人做的工具这样的小事就足够了，这样可以让工具更容易够到。

我还想对那些和 [remove.bg](https://www.remove.bg/) 一起工作的人大声疾呼，感谢他们让我使用他们的标志，感谢他们抽出时间回复某个随机制作了 vscode 插件的瑞典人。