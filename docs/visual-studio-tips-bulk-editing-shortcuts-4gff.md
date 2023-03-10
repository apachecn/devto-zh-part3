# Visual Studio 提示-批量编辑快捷键

> 原文：<https://dev.to/hutchcodes/visual-studio-tips-bulk-editing-shortcuts-4gff>

作为开发人员，我们做的最单调乏味的事情之一就是不断重复修改。也许我们需要在单个对象上设置一些属性，或者也许我们想要在一堆测试对象的实例化中添加一个属性。这里有一些捷径可以帮助你做到这一点。

### 多插入符号编辑

让我们从向一些测试对象的实例化添加一个属性开始。这通常是“多插入符号编辑”的完美场景为此，你可以做两件事。第一步是将插入符号定位在第一行中您想要开始编辑的位置，然后按住`Shift+Alt`并使用箭头或鼠标选择一列或一个区域。这种方法的美妙之处在于，它允许您选择要替换的文本块。

[![alt text](img/b6d62be1e49a892bc44a88276f55de51.png "Using Shift Alt to replace text on multiple lines")](https://res.cloudinary.com/practicaldev/image/fetch/s--LNQo4XOz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/MultiCaratReplace.gif)

使用`Shift+Alt`的缺点是你想要编辑的列必须垂直排列。但通常情况并非如此，这就是`Ctrl+Alt`发挥作用的地方。使用`Ctrl+Alt`，您可以通过按住`Ctrl+Alt`并点击您想要编辑的位置来设置多个目标。然后，您可以在所有这些位置插入文本，甚至在同一行多次插入。

[![alt text](img/b2fd99b52618c039916a65d36137f9cf.png "Using Control Alt to insert text on multiple lines")](https://res.cloudinary.com/practicaldev/image/fetch/s--gkWrsnLJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/MultiCaratInsert.gif)

### 重复行

如果您想要编辑的行已经存在，或者如果您知道需要一次创建多少行，那么这些方法非常有用。但是我经常发现自己想要复制一行并对其进行一些修改。为此，您可以使用`Ctrl+E, Ctrl+V`键盘快捷键，该快捷键复制光标所在的行。

[![alt text](img/ef2fa11d13e2c6d7c503ef952fe7a1d4.png "Duplicate line with Control E, Control V")](https://res.cloudinary.com/practicaldev/image/fetch/s--TsOcFpaj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/DuplicateLine.gif)

### 剪贴板响起

我发现另一个在我反复编辑时非常有用的功能是剪贴板环。当你点击`Ctrl+Shift+V`时，它会调出你的剪贴板循环，其中包括你最近复制或剪切的九个内容(最近在顶部使用)。然后，您可以通过键入数字、使用箭头键或单击鼠标来选择要粘贴的项目。

[![alt text](img/4b6dbbdeb4d483a415e1dafd7a69b3ab.png "Using Clipboard Ring")](https://res.cloudinary.com/practicaldev/image/fetch/s--66FDCoxo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/ClipboardRing.gif)

当您需要将多个内容从一个文件复制到另一个文件时，这很有用。你不再需要来回翻动，只需要复制，复制，粘贴，粘贴。

### 将文件作为文本插入

这最后一个功能是我在编辑菜单中查找时偶然发现的。我不指望会经常用到它，但它可能会有用。在“编辑”菜单中有一个“以文本形式插入文件”的命令，这个命令就是它所说的。

[![alt text](img/4eaeff2702758756dc28c4f116cc9bc7.png "Using Edit, Insert File as Text to insert a file as text")](https://res.cloudinary.com/practicaldev/image/fetch/s--vHlDEhbm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/InsertFileAsText.gif)