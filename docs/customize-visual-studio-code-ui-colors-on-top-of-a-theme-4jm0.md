# 在主题上自定义 Visual Studio 代码 UI 颜色

> 原文：<https://dev.to/tluanga34/customize-visual-studio-code-ui-colors-on-top-of-a-theme-4jm0>

你是否仅仅因为某个 UI 的外观就对自己喜欢的 vscode 主题不满意？我也是这种情况。有时候我想增加文字和背景的对比。

在本文中，我将向您展示如何在不编写任何高级代码的情况下定制 vscode 窗口的每个元素。

我们要做的是，我们将安装并破解一个名为窗口颜色的 vscode 扩展。

扩展网址:[https://marketplace.visualstudio.com/items?itemName = Stuart . unique-window-colors](https://marketplace.visualstudio.com/items?itemName=stuart.unique-window-colors)

这个扩展的主要目的(我猜)是为每个窗口分配一个独特的颜色，这样当你使用 alt-tab 键时，你就可以识别出每个窗口，这个功能真的很棒。

安装后，该扩展将创建。vscode 文件夹，并在该文件夹中创建 settings.json 文件。你可以提交这个文件，也可以不提交，这取决于你的意愿。我的建议是忽略。全局 gitignore 文件中的 vscode 文件夹。

现在让我们深入研究新创建的。vscode 文件夹。

您将看到如下所示的 json

T2`"workbench.colorCustomizations": {
"activityBar.background": "#13332E",
"titleBar.activeBackground": "#19423B",
"titleBar.activeForeground": "#F6FBFB"
}`

现在转到[https://code.visualstudio.com/api/references/theme-color](https://code.visualstudio.com/api/references/theme-color)并尝试该页面中列出的任何可定制的属性。您将看到大量可以添加到这个 json 中的属性。

比如改变编辑器背景，我只需添加“editor . background”:“# 020531”。

要更改侧边栏背景，我只需添加“sidebar . background”:# 3d 003 f”。

那是简单的权利。这就是我现在能分享的。

祝你愉快。