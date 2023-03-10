# 从 PowerShell 将多个单元格粘贴到 Excel 中

> 原文：<https://dev.to/jermdavis/pasting-multiple-cells-into-excel-from-powershell-3hba>

有时候，在行为不端的 Sitecore 服务器上工作的教训与 CMS 无关。最近，我在解决其他一些问题时，学到了一些关于 Excel 的有用知识。不确定这是否“如此简单，我只是最后一个意识到”或者这是否是一个真正有用的小细节——只是因为其他人可能会受益:

我最近在监控一个 Sitecore 服务器的问题，为了跟踪发生了什么，我在 Excel 中做了一个图表。我时常会在服务器上查看某个数据值，然后将它和测量的日期/时间一起复制/粘贴到电子表格中。

这样做了几次后，我被让我的生活更简单的冲动所打动。由于… [原因](https://media.giphy.com/media/9rAqbQNmPnQm9BRwsw/giphy.gif) …让 PowerShell 一次写出一个 CSV 文件并导入 Excel 是不现实的，所以我可以让 PowerShell 给我剪贴板上的日期和值吗，这样我就可以把它粘贴到我的电子表格中了？

是的，当然:

首先，获取当前日期/时间是由“[`Get-Date`](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-date?view=powershell-6)”command let 处理的。碰巧我使用的服务器是为美国地区配置的，作为一个英国人，这意味着我希望我的日期格式与默认输出不同。没问题——你只需要告诉 PowerShell 你希望你的约会使用 standard。Net 格式字符串数据:

```
$timeStamp  =  Get-Date  -Format  "dd/MM/yyyy hh:mm" 
```

Enter fullscreen mode Exit fullscreen mode

获取实际测量值在这里并不相关，但是我也将数据存储在一个变量中:

```
$value  =  /\*  someMagicToMeasureAValue  \*/ 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用“[`Set-Clipboard`](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/set-clipboard?view=powershell-5.1)”command let 将内容自动发送到剪贴板。

但是我的电子表格的格式是一列表示时间戳，一列表示值，所以我可以根据数据绘制图表。那么，如何将数据以正确的格式放到剪贴板上，以便粘贴到 Excel 中确保数据进入两个单元格呢？

在谷歌上玩了一会儿之后，现在回想起来，这一点很明显:你只需要用制表符分隔你的数据。在 PowerShell 中，“` t”(反勾号，后跟“t”)是一个制表符。这意味着我可以用:
将我的两列数据发送到剪贴板

```
"$timestamp  `t  $value"  |  Set-Clipboard 
```

Enter fullscreen mode Exit fullscreen mode

如果我点击粘贴到 Excel，我会得到:

[![](img/c4530b440e759596802290dc750d7090.png)](https://jermdavis.files.wordpress.com/2019/03/pastetwocells.png)

如果你想粘贴多列，你可以放入多个标签…