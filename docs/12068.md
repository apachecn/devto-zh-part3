# 新版本-用孔雀 0.3.0 选择你命名的颜色

> 原文：<https://dev.to/john_papa/new-release---choose-your-named-color-with-peacock-020-5415>

感谢伟大的 OSS 社区帮助解决孔雀的问题和请求！自从我上周发布了 [Peacock](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa) 以来，我已经对它进行了一些改进。

当我宣布的时候，它似乎很受欢迎(感谢支持)。我通过 Twitter 和 GitHub 收到了一些好的建议/请求。所以我决定加几个。

[![peacock icon](img/86ac58c8159605566a1a94acc4ef3145.png)](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa)

## 最新消息

以下是新特性:(或者你可以查看一下 [CHANGELOG.md](https://github.com/johnpapa/vscode-peacock/blob/master/CHANGELOG.md) )

最大的新功能是...

*   您可以选择指定的颜色(例如紫色、道奇蓝、灰色)

*   新气象

*   您可以重置(也称为清除)Peacock 在工作区中设置的所有颜色。

*   你可以告诉 Peacock，当你选择一种颜色时，VS 代码的哪些部分会受到影响。您可以通过将属性`peacock.affectedSettings`设置为下面的一个或多个有效值来做到这一点。

```
 // Valid settings you can choose to be affected
  "peacock.affectedSettings": [
      "activityBar",
      "statusBar",
      "titleBar"
    ] 
```

*   设置并选择您最喜欢的颜色偏好

以下是您可以设置的所有自定义。

| 财产 | 描述 |
| --- | --- |
| 孔雀.受影响的元素 | 受孔雀影响的元素的前缀 |
| 孔雀。黑暗前景 | 黑暗前景的覆盖 |
| peacock . light 前景 | 灯光前景的覆盖 |
| 孔雀。喜欢的颜色 | 颜色名称和十六进制值的字符串数组 |

在用户设置中为`peacock.preferredColors`设置一种或多种颜色(十六进制或命名)后，您可以选择`Peacock: Change to a Preferred Color`，系统将从用户设置中提示您来自`peacock.preferredColors`的列表。

```
"peacock.preferredColors": [
  "purple",
  "#102030",
  "dodgerblue"
] 
```

## 得到孔雀

如果你有孔雀并且想要更新，VS 代码会很快提示你。

如果你有兴趣试用孔雀，你可以在这里的市场找到它。它目前正在预览中，这意味着前面可能有龙。

*   在这里获取扩展名
*   贡献给 GitHub 库[这里](https://github.com/johnpapa/vscode-peacock?wt.mc_id=devto-blog-jopapa)

最坏的情况是，这个扩展只是我将使用的东西，这没关系。但如果你也喜欢，请试一试，并在 GitHub 中提交反馈。您可以[打开问题](https://github.com/johnpapa/vscode-peacock/issues?wt.mc_id=devto-blog-jopapa)或抓住[打开问题](https://github.com/johnpapa/vscode-peacock/issues?wt.mc_id=devto-blog-jopapa)并帮助投稿。

谢谢！