# 在 VS 代码中显示读取你的降价的时间

> 原文：<https://dev.to/azure/show-the-time-to-read-your-markdown-in-vs-code-2p9l>

## 读取时间

最近我发布了[我如何写在线文章](https://dev.to/azure/how-i-write-online-articles-1lfb)。从那以后，我一直在想办法让这变得更简单。今天我宣布一个新的 VS 代码扩展的预览版本，叫做[读取时间](https://marketplace.visualstudio.com/itemdetails?itemName=johnpapa.read-time&wt.mc_id=devto-blog-jopapa)

> 你可以在这里从 VS 代码市场获得[读取时间](https://marketplace.visualstudio.com/itemdetails?itemName=johnpapa.read-time&wt.mc_id=devto-blog-jopapa)

Read Time 是一个 Visual Studio 代码扩展，非常适合那些希望估计阅读 markdown 需要多长时间的作者。

[![john_papa image](img/e4bea158821130090634a4a56bffc625.png)](/john_papa) [## 我如何写在线文章

### 约翰爸爸 1914 年 4 月 1 日阅读

#devrel #vscode #markdown #discuss](/azure/how-i-write-online-articles-1lfb)

当你写 markdown 的时候，你会在你的 VS 代码状态栏中看到一个书的图标和一个数字。当您在减价文件中添加或删除文本时，该数字将会更新。当你不在减价文件中时，它就会消失。就这么简单。

[![read-time-ex](img/ec71a46794bc4cb05590b87e2b549820.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z_xTO-ci--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5v6zwyana94fxmc3r7d3.jpg)

## 未来想法

这个扩展是新的，我有一些想法如何使它更加有用。如果您有想法，请在这里或 GitHub 中分享您的反馈。

我想添加的一个新功能是调整一个人阅读的假设每分钟字数的能力。默认情况下，在许多计算中，这是 200 字/分钟。API 为此是开放的，我已经在库中做了一个 PR 来调整它。我添加这个功能只是时间问题。

另一个是允许调整它将读取的文件类型。目前，它只能读取降价文件。但是这也可以扩展到其他文件。让它成为用户配置的特性会很好。

## 为什么图标没有出现在状态栏中？

状态栏中只有这么多的空间，阅读时间状态栏图标可能会成为牺牲品，从你的状态栏中消失。您可以通过缩小几次 vs 代码来测试这一点。缩小也会缩小状态栏。如果状态栏图标是一个牺牲品，那么当你缩小时它会重新出现。

读取时间图标非常纤细(只有一个图标和一个数字)。但是，如果它没有出现，您可以尝试更改状态栏中其他项目的设置，使它们消失。换句话说，通过隐藏你不想看到的东西来清理你的状态栏。这里有一个例子，我在状态栏中隐藏了一些东西。

```
 "workbench.statusBar.feedback.visible":  false,  "debug.showInStatusBar":  "never",  "azure.showSignedInEmail":  false,  "liveshare.showInStatusBar":  "whileCollaborating",  "spellright.statusBarIndicator":  false 
```

## VS 代码扩展编写

如果你好奇，这里是我使用的 VS 代码扩展:

*   [降价皮棉](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint&wt.mc_id=devto-blog-jopapa)
*   [拼写正确](https://marketplace.visualstudio.com/items?itemName=ban.spellright&wt.mc_id=devto-blog-jopapa)
*   [孔雀](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa) -我用彩色编码我的 vs 代码编辑器写文章
*   [读取时间](https://marketplace.visualstudio.com/items?itemName=johnpapa.read-time&wt.mc_id=devto-blog-jopapa)

## 你怎么看？

我很想听听你的反馈。你喜欢它的什么？还有什么对你写作有用的？