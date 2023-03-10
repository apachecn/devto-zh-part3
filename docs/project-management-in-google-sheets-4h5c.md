# Google Sheets 中的项目管理

> 原文：<https://dev.to/rick_viscomi/project-management-in-google-sheets-4h5c>

Google Sheets 可以成为项目管理的便捷工具。我想写这篇文章来分享一些在工作表中构建甘特图的技巧。
*注意:这是基于我在表单中找到的一个[模板](https://docs.google.com/spreadsheets/d/1pZMcre0cNnWQhhxRYIdlaSvlTpe4rPNFsez1B9N_7Cw/copy?usp=sharing)，我将其修改为更具动态性。*

## 1。时间线

[![timeline row in Sheets](img/76cd100732e3d2b8f98ed3166a969950.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lJBAV179--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ksr34wjj46hnjzvq022d.png)

您可能已经知道，像大多数电子表格工具一样，Sheets 具有原生日期支持。因此，您可以输入类似“2019 年 3 月 4 日”的文本，工作表会将其理解为 2019 年 3 月 4 日(抱歉，非美国人)。

您可能不知道的是，您可以在日期上应用算术运算，比如给日期加上天数。因此，对于我们的时间线，我们可以从一个固定的日期开始，比如 3 月 4 日，然后通过添加 7 来动态创建下周的日期。在上面的例子中，3 月 4 日是在细胞 E3。所以下周的日期将在 F3 中，我们可以使用公式`=E3+7`来生成它。复制 F3，选择该行的其余部分，并选择`Edit > Paste special > Paste formula only`为时间线的其余部分生成日期。

[![custom date format](img/eaddd390da564b374917477d72c1a711.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eJ1T_7RR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8vf42gsplriv7sobn3ri.png)

要使日期只显示为一个月中的某一天，可以使用自定义日期格式。选择时间轴单元格，转到`Format > Number > More formats > More date and time formats...`，仅将“日”芯片放入表单字段。

## 2。当前周

[![current week highlighted](img/71394fd00e08074dd26168727d776b34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CGSxmwbT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h8kbsdraj921ztfiozt4.png)

您会注意到在前面的截图中，4 月 22 日以黄色突出显示。今天的日期是 4 月 25 日，所以这表明我们目前在 22 日这一周。

这要归功于条件格式。格式规则包括三个与日期相关的值:日期是、日期之前和日期之后。因此，当单元格的日期值在过去 7 天内时，工作表可以动态地更改单元格的背景颜色。

要创建此规则，请选择时间线行中的所有单元格，转到`Format > Conditional formatting...`，并将规则设置为“日期在过去一周内”。在我的例子中，我应用了粗体和黄色背景颜色格式。

因此，每次你打开工作表时，本周的截止日期应该是很明显的。

## 3。项目网格

[![grid of filled cells](img/80136d79d3e1ae1e936c826684d498a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--swauEpBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jd7h2kv47lkgi42xd1h.png)

这更像是一个快速提示，但是如果您手动处理每一行和每一列，让项目网格由正方形组成可能会很乏味。

要将行和列设置为完全相同的高度/宽度，首先选择所有列，右键单击，选择`Resize columns...`，并以像素为单位输入列宽。我用的是 26px。然后对所有行重复上述步骤。它们不必是完美的正方形，只要所有的行都是相同的高度，所有的列都是相同的宽度。

剩下的魔术是应用边框和背景样式，使单元格看起来像瀑布图中的块。这可能很乏味，但是复制单元格并选择`Edit > Paste special... > Paste format only`可能是在许多单元格中重复相同样式的有用方式。

如果你想复制我的工作表作为起点，你可以在这里找到它。希望你觉得这有用！