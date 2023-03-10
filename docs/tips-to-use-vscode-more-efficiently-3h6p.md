# 更有效地使用 VSCode 的技巧

> 原文：<https://dev.to/selrond/tips-to-use-vscode-more-efficiently-3h6p>

假设您已经使用 VSCode 有一段时间了。
你已经改变了颜色主题(如果没有，我强烈推荐[材质主题](https://material-theme.site/))，你已经调整了一些基本的设置并安装了一些流行的扩展。

也许你足够了解如何完成这项工作。这很好，但是很有可能你会错过它的一些特性。

这是一个设置、扩展和快捷方式的集合，证明对我作为一名 web 开发人员的工作特别有用。

### jsconfig.json

VSCode 最容易被忽视的基本特性之一是`jsconfig.json`。如果在 VSCode 中打开 JS 项目，它不知道其中的文件是相关的。它将每个文件视为一个独立的单元。
您可以通过在项目的根目录下创建`jsconfig.json`文件来告诉它您的项目。

`jsconfig.json`(除其他外)支持智能转到定义，它与各种模块解析算法一起工作。在实践中——这意味着你现在可以在代码中的各种引用上`⌘ click`,它会带你到它们的定义。我强烈推荐你[阅读更多关于它的内容](https://code.visualstudio.com/docs/languages/jsconfig)，但这是我最常使用的:

```
{
  "compilerOptions": {
    "baseUrl": "src/", 
    "target": "esnext",
    "module": "commonjs"
  },
  "exclude": [
    "node_modules",
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 一个设置引物

*注意:如果您已经知道在哪里可以找到 VSCode 设置以及如何编辑它们，请跳到下一节*

VSCode 将设置存储在一个类似 JSON(即所谓的 [`jsonc`](https://code.visualstudio.com/docs/languages/json#_json-with-comments) -带注释模式的 JSON)的文件中。可以通过`⌘ ,`快捷方式访问，也可以通过`Code > Preferences > Settings`访问。([点击此处](https://code.visualstudio.com/docs/getstarted/settings)了解有关 VSCode 设置的更多信息)

打开设置后，您不会马上看到原始的 JSON。VSCode 最近为它们提供了一个很好的用户界面，但是出于本文的目的，以原始键值形式共享设置更容易，所以我们不会使用它。

您可以通过点击选项卡栏中的`{ }`图标来访问设置 JSON。

如果它是空的(您还没有对默认设置进行任何修改)，让我们创建一个空对象，这样它就是一个有效的 JSON:

[![VSCode settings](img/d5acdbb4d26e251f45d85ffe0245a526.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MkzARmBF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s0uxudg38xzlz35n7uti.png)

### 主题

这看起来很基本，但并不意味着它不重要。你花了很多时间看代码，所以你也可以花一些时间选择一个让你看起来舒服的主题，并且让代码看起来漂亮。

正如我已经提到的，我使用的是[材质主题](https://material-theme.site/)海洋高对比度变体。这些年来我尝试了很多，但最终还是选择了这一个。

还有一点——那些好看的文件/文件夹图标是通过[素材主题图标](https://marketplace.visualstudio.com/items?itemName=Equinusocio.vsc-material-theme-icons)扩展实现的:

[![Customized sidebar](img/093c8322470a02fa8d507f6d2c4f2880.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VnVipnvh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2tory4tjc7u3sm66cz9u.png)

这是您的设置(和编辑器)现在的样子:

[![Edited settings](img/7257e49ed64aafe0ec429adfebc1481d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QZEevqnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0b487t20eoalvyj4xw6m.png)

不错吧？

快速提示:您可以通过在命令调板中搜索“accent”来更改材质主题的强调颜色。

### 字体

正确的字体可以让你的代码更加清晰易读。我选择的编程字体是[`Fira Code`](https://github.com/tonsky/FiraCode)——一种健壮的&制作精良的编程字体，有着漂亮的连字。[试试看](https://github.com/tonsky/FiraCode/wiki/VS-Code-Instructions)！我说过它是免费的吗？

### 缩进

在“制表符与空格”的争论中，无论你站在哪一边，你都可以这样设置:

```
"editor.detectIndentation":  true,  "editor.insertSpaces":  true  "editor.tabSize":  2 
```

Enter fullscreen mode Exit fullscreen mode

### 在编辑器和浏览器之间切换

您可以使用`⌘ ⇧ E`快捷方式在代码编辑器和项目文件浏览器之间轻松切换。当你在浏览器中时，你可以使用与 MacOS finder 相同的键进行常规操作——箭头键用于导航,`↵`用于重命名文件/文件夹,`⌘ ↓`用于打开当前文件。

快速提示:使用`⌥ ⌘ R`在本机 MacOS finder 中显示聚焦的文件/文件夹。

### 埃米特

Emmet 是许多流行文本编辑器的插件，它通过启用巧妙的缩写、扩展、常见动作(将元素包装在其他元素中)等等，极大地改进了 HTML & CSS 工作流程。你可能会说你不是在直接编写 html，但它可以很容易地配置成与 React 和 Vue 等框架配合使用，因为它们使用类似 HTML 的标记。

VSCode [自带 Emmet](https://code.visualstudio.com/docs/editor/emmet) 支持`html`、`haml`、`jade`、`slim`、`jsx`、`xml`、`xsl`、`css`、`scss`、`sass`、`less`和`stylus`文件。

因此，默认情况下，你必须使用`.jsx`文件扩展名，以获得 Emmet 对 JSX 文件的支持。假设您只处理`.js`文件，那么您有两个选择:

1.  告诉 Emmet 在`.js`文件中运行:

```
"emmet.includeLanguages":  {  "javascript":  "javascriptreact",  } 
```

Enter fullscreen mode Exit fullscreen mode

(在`javascript`文件中启用`javascriptreact` Emmet 语法)

1.  告诉 VSCode 像对待`.jsx`一样对待任何`.js`文件(这意味着对所有`.js`文件也使用`javascriptreact`语法)，这样 Emmet 就把 is 看作是一个`.jsx`文件:

```
"files.associations":  {  "*.js":  "javascriptreact"  } 
```

Enter fullscreen mode Exit fullscreen mode

我选择了第二个——我从来不使用`.jsx`文件扩展名，所以我想让 VSCode 在`.js`文件中支持 React。

这些 Emmet 命令是我最常用的命令:

*   `expand abbreviation` -将字符串扩展为 JSX 元素
*   `wrap with abbreviation` -用另一个元素包装现有的 JSX
*   `split / join tag` -从标签对(例如从`expand abbreviation`的输出)制作自结束标签，反之亦然

Emmet 真的很强大，可以节省你很多时间，所以我强烈推荐你去 emmet.io 网站上看看他们的演示。

### 快速打开真实文件

让我们使用快速打开命令打开一个文件:`⌘ P`。

注意标签栏——用斜体书写的文件名意味着标签处于*预览*模式。默认情况下，如果您从侧边栏或快速打开(`⌘ P`)中选择文件，然后选择/快速打开另一个文件，它会重复使用预览选项卡，直到它被*锁定*(双击，或者文件被编辑)。

如果你正在浏览侧边栏中的文件，这种行为是有意义的，可能只是偷看文件，但是如果你想快速打开某个东西，你可能知道你想“真正地”打开它。

为此，设置如下:

```
"workbench.editor.enablePreviewFromQuickOpen":  false 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试`⌘ P` -您的文件将不再以预览模式打开。

### 面包屑

[![Breadcrumbs](img/51b22e31c7fb3f30f575e4448ac05be1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0KuMQba4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6phraa6tenihp5byg8ua.png)

面包屑(显示在标题栏下)是 VSCode 的一个有用特性，它显示了您在代码库中的位置。如果你点击其中一个部分，它会显示你当前的位置，以及同一层的文件/符号，也可以作为快速导航。

使用此设置启用它们:

```
"breadcrumbs.enabled":  true 
```

Enter fullscreen mode Exit fullscreen mode

谈到面包屑，有两个有用的快捷方式:

*   聚焦面包屑:它将选择最后一个元素并打开一个下拉菜单，允许你导航到一个兄弟文件或符号。
*   聚焦面包屑的最后一个元素而不打开它，这样你就可以用箭头在路径层次中移动。

快速提示:您可以在面包屑弹出窗口中键入内容来过滤文件和文件夹/符号，并使用`↵`来关注它们。

### 隐藏打开的编辑部分

你可以在标签页中看到打开的文件。

```
 "explorer.openEditors.visible":  0 
```

Enter fullscreen mode Exit fullscreen mode

### 自定义标题栏

默认的 VSCode 标题不是很有用。它只显示当前文件名和项目名。以下是你可以改进的方法:

```
"window.title":  "${dirty} ${activeEditorMedium}${separator}${rootName}" 
```

Enter fullscreen mode Exit fullscreen mode

*   `${dirty}`:如果活动编辑器是脏的，则为脏指示器。
*   `${activeEditorMedium}`:文件相对于工作区文件夹的路径(如`myFolder/myFileFolder/myFile.txt`)
*   `${separator}`:条件分隔符(“-”)，仅在被带有值的变量或静态文本包围时显示。
*   `${rootName}`:工作区的名称(如 myFolder 或 myWorkspace)。

您可以在这里看到所有可用的选项[。](https://code.visualstudio.com/docs/getstarted/settings)

### 小地图

你可能知道 Sublime Text 中著名的 minimap widget。默认情况下它是打开的，但是看起来很糟糕:

[![Default minimap](img/0651008e710fbd1890825c07e7de552f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BANpIC9g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wpsz57yok5qeemn8dh5t.png)

让我们改进它。

首先，让我们用色块代替缩小的字符。然后设置最大水平列，最后，总是显示“滑块”,这样我们就可以浏览小地图，看看屏幕在文件中的位置。

```
"editor.minimap.renderCharacters":  false,  "editor.minimap.maxColumn":  200,  "editor.minimap.showSlider":  "always" 
```

Enter fullscreen mode Exit fullscreen mode

[![Customized minimap](img/968b665dfa5aa9e01d44c6e3b1ceb619.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KxWSYqy_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5i3wzav3dxsxq2vp8wfq.png)

### 空白

你可能想看所有的角色:

```
"editor.renderWhitespace":  "all" 
```

Enter fullscreen mode Exit fullscreen mode

### 平滑滚动

```
"editor.smoothScrolling":  true 
```

Enter fullscreen mode Exit fullscreen mode

### 插入符号改进

关于光标的相位而不是闪烁，有一种奇怪的满足感:

```
"editor.cursorBlinking":  "phase" 
```

Enter fullscreen mode Exit fullscreen mode

此外，当光标移动时有轻微的动画效果，光标更容易跟随:

```
"editor.cursorSmoothCaretAnimation":  true 
```

Enter fullscreen mode Exit fullscreen mode

### 最终新行

在你的文件末尾加上新的一行
是一种常见的做法

```
"files.insertFinalNewline":  true 
```

Enter fullscreen mode Exit fullscreen mode

### 修剪尾部空白

```
"files.trimTrailingWhitespace":  true 
```

Enter fullscreen mode Exit fullscreen mode

### [遥测](https://code.visualstudio.com/docs/supporting/FAQ#_how-to-disable-telemetry-reporting)

我喜欢禁用遥测:

```
"telemetry.enableCrashReporter":  false  "telemetry.enableTelemetry":  false, 
```

Enter fullscreen mode Exit fullscreen mode

此外，还有默认启用的[自然语言搜索](https://code.visualstudio.com/blogs/2018/04/25/bing-settings-search)，当搜索设置时，[会将你的击键](https://github.com/Microsoft/vscode/issues/49161)发送到 Bing。如果你也想关掉它，把它添加到你的设置中:

```
"workbench.settings.enableNaturalLanguageSearch":  false 
```

Enter fullscreen mode Exit fullscreen mode

### 复制文件路径

当谈到代码时，能够指向一个特定的文件通常是很有用的。VSCode 提供了通过命令面板`⌘ P`复制活动文件的绝对和相对文件路径，但是[设置自己的键盘快捷键](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-editor)会更快。

用`⌘ K, ⌘ S`打开你的快捷键文件(commad + K 紧跟着 commad + S)，然后添加以下内容(或者你喜欢的任何组合键)

复制相对路径

```
{  "key":  "shift+cmd+c",  "command":  "copyRelativeFilePath"  } 
```

Enter fullscreen mode Exit fullscreen mode

复制绝对路径

```
{  "key":  "shift+alt+cmd+c",  "command":  "copyFilePath"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 隐藏底部栏中的反馈笑脸

```
"workbench.statusBar.feedback.visible":  false 
```

Enter fullscreen mode Exit fullscreen mode

## 分机

[丰富的扩展生态系统](https://marketplace.visualstudio.com/)是 VSCode 腾飞的原因之一。以下是证明自己有用的方法:

*   [设置同步](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)
*   [巴别塔 JavaScript](https://marketplace.visualstudio.com/items?itemName=mgmcdermott.vscode-language-babel)
*   [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
*   [路径智能感知](https://marketplace.visualstudio.com/items?itemName=christian-kohler.path-intellisense)
*   [vs code-style-components](https://marketplace.visualstudio.com/items?itemName=jpoissonnier.vscode-styled-components)
*   [GitLens — Git 增压](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
*   [LintLens — ESLint 规则变得更简单](https://marketplace.visualstudio.com/items?itemName=ghmcadams.lintlens)
*   [DotENV](https://marketplace.visualstudio.com/items?itemName=mikestead.dotenv)
*   [导轨](https://marketplace.visualstudio.com/items?itemName=spywhere.guides)
*   [括号对上色器](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer)
*   [ES7 React/Redux/graph QL/React-Native 片段](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets)
*   [高级新文件](https://marketplace.visualstudio.com/items?itemName=dkundel.vscode-new-file)
*   [重复动作](https://marketplace.visualstudio.com/items?itemName=mrmlnc.vscode-duplicate)
*   [颜色高亮](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)
*   git ignore
*   [待办事项高亮显示](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight)
*   [对 JSON 对象进行排序](https://marketplace.visualstudio.com/items?itemName=richie5um2.vscode-sort-json)
*   [VS 代码的编辑器配置](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)
*   [图像预览](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.vscode-gutter-preview)
*   [一体降价](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)
*   [降价预览 Github 样式](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-preview-github-styles)
*   [对齐](https://marketplace.visualstudio.com/items?itemName=steve8708.Align)
*   [HTML CSS 支持](https://marketplace.visualstudio.com/items?itemName=ecmel.vscode-html-css)
*   [排序行](https://marketplace.visualstudio.com/items?itemName=Tyriar.sort-lines)
*   [切换引号](https://marketplace.visualstudio.com/items?itemName=BriteSnow.vscode-toggle-quotes)
*   [版本镜头](https://marketplace.visualstudio.com/items?itemName=pflannery.vscode-versionlens)
*   [Visual Studio IntelliCode -预览](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)
*   [VS 直播分享](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare)
*   [波兰语](https://marketplace.visualstudio.com/items?itemName=pnp.polacode)

如果有什么让你对 VSCode 的体验特别好，请在评论中分享！

*首发于[sudo labs . io](https://sudolabs.io/blog/tips-to-use-VSCode-more-efficiently/)T3】*