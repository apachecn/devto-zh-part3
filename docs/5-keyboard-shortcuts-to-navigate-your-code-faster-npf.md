# 5 个键盘快捷键，可以更快地浏览代码

> 原文：<https://dev.to/owenconti/5-keyboard-shortcuts-to-navigate-your-code-faster-npf>

注:这是我博客上的一篇交叉文章。在这里找到原文:[https://Owen conti . com/tips/5-keyboard-shortcut s-to-navigate-your-code-faster/](https://owenconti.com/tips/5-keyboard-shortcuts-to-navigate-your-code-faster/)T3】

我也有这篇文章的视频版本:[https://dev . to/Owen conti/video-follow-up-5-keyboard-shortcut s-to-navigate-your-code-faster-2mne](https://dev.to/owenconti/video-follow-up-5-keyboard-shortcuts-to-navigate-your-code-faster-2mne)

这里有 5 个键盘快捷键，你可以在任何 IDE 中实现，帮助你更快地浏览你的代码。

我使用 VS 代码作为我的 IDE，但是这些快捷方式可以应用于任何编辑器。

我在本文中列出的键盘快捷键是 OSX 特有的，但是如果您替换:

*   **CMD** 带 **CTRL**
*   **选项**带 **ALT**
*   **CTRL** 保持为 **CTRL**

*前言:这些快捷键可能不适用于 Vim 等一些编辑器。*

## 删除行

在使用删除行快捷方式之前，您会发现自己高亮显示了一整行，然后按 backspace 或 delete。这是可行的，但是捷径可以让它更快。如果您突出显示了多行，此快捷方式也适用于多行。

*   VSCode 默认: **Shift + CMD + K**
*   我的按键绑定: **CMD + D**

我更喜欢我的键绑定而不是 VS 的默认代码，因为我可以用我的左手按它。

## 移动线条

您是否经常复制/粘贴行，以便在一行中上下移动它们？使用这个键盘快捷键，您可以简单地使用箭头键来移动线条。

*   VS 代码默认:**选项+上/下箭头键**
*   我的键绑定:**与 VS 代码默认**相同

我坚持使用默认的 VS 代码，因为这是我开始使用的。

## 去行

很多人没有意识到几乎所有的文本编辑器都有内置的跳转行工具。当您处理较大的文件并需要找到特定的行(可能来自调试时堆栈跟踪的结果)时，这非常有用。

*   VS 代码默认: **CTRL + G**
*   我的按键绑定: **CMD + L**

我更喜欢我的键绑定，因为“Line”是以“L”开头的，所以对我来说，记住 **CMD + L** 比记住 **CTRL + G** 更容易。

## 后退/前进

让我们假设您打开了 5 个文件，并且您正试图跟踪文件之间的代码流。现在你想回到上一个文件，重新读一行，但是你不记得是哪个文件，也不记得是哪个行号。

这个快捷方式会将光标移动到之前/之后的位置，甚至可以跨文件移动。把它想象成光标的“撤销/重做”。

**回去**

*   VS 代码默认: **CTRL + -**
*   我的按键绑定: **CMD + [**

**前进**

*   VS 代码默认: **CTRL + SHIFT + -**
*   我的按键绑定: **CMD + ]**

我更喜欢我的键绑定，因为它是 Eclipse 中的默认设置，这也是我学习这个快捷方式的地方。我也更喜欢****的区别，而不是用 **SHIFT** 作为修饰语。****

 ****## 字的开始/结束

这是一个游戏改变者。有多少次你发现自己按住左箭头键，而光标慢慢移向行的中间，以便你可以修复一个打字错误或替换一个单词？

使用单词的开始/结束快捷方式(有时称为**光标单词开始/结束**)，您可以快速地在一行中一次向左/向右导航一个单词，而无需按住箭头键或使用鼠标。

***注意:**下划线被认为是单词的一部分，而连字符不是。*

*   VS 代码默认:**选项+左/右箭头键**
*   我的键绑定:**与 VS 代码默认**相同

## 奖励快捷键！

我包含了两个额外的快捷方式，它们非常类似于单词开始/结束的快捷方式。

## 行的开始/结束

除了 word 的开头/结尾，还可以通过使用 **CMD** 代替**选项:**快速转到一行的开头/结尾

*   **CMD +左/右箭头键**

## 高亮显示您使用的字/行的开始/结束字符

假设你有一些类似这样的代码，你的光标在单词“world”的末尾:

```
const name = "Owen";
console.log(`Hello world!`); 
```

Enter fullscreen mode Exit fullscreen mode

现在您想用`name`变量替换“world”。您有两个选项可以快速选择“世界”:

1.  用鼠标双击“世界”🤮
2.  使用 word 的开始/结束快捷方式快速突出显示“world”

使用快捷键，你可以按住 **SHIFT + OPTION** ，然后点击**左箭头**键来高亮显示世界。这将突出显示单词“world”，然后可以用变量`name`替换它。

## 总结

为了总结我们今天学到的东西，我推荐如下:

*   删除行: **CMD + D**
*   移动行:**选项+向上或向下箭头**
*   转到行: **CMD + L**
*   后退/前进: **CMD +【或】**
*   单词的开始/结束:**选项+向左或向右箭头**
*   突出显示以单词开头/结尾的字符: **SHIFT + OPTION +向左或向右箭头**

## VS 代码 keybindings.json 文件

如果你想看看我其余的键绑定，这里有我的 VS 代码的`keybindings.json`文件的副本:

```
[
  {
    key: "cmd+d",
    command: "editor.action.deleteLines",
    when: "editorTextFocus"
  },
  {
    key: "cmd+t",
    command: "workbench.action.quickOpen"
  },
  {
    key: "cmd+[",
    command: "workbench.action.navigateBack"
  },
  {
    key: "cmd+]",
    command: "workbench.action.navigateForward"
  },
  {
    key: "shift+cmd+b",
    command: "workbench.action.tasks.runTask"
  },
  {
    key: "alt+`",
    command: "workbench.action.terminal.focusPrevious"
  },
  {
    key: "ctrl+cmd+a",
    command: "-extension.align",
    when: "editorTextFocus"
  },
  {
    key: "cmd+\\",
    command: "workbench.files.action.showActiveFileInExplorer"
  },
  {
    key: "cmd+l",
    command: "workbench.action.gotoLine"
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode****