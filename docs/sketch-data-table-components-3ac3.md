# 草图数据表组件

> 原文：<https://dev.to/whoisryosuke/sketch-data-table-components-3ac3>

表格——它们包含了我们经常使用的大量不同的接口。电子表格、CRM、设置页面——我们在用户界面中随处可见表格，作为一名设计师，不可避免地要模拟一个(不要完全用 Excel/HTML 的截图)。

我必须在 Sketch 中为一个客户设计一个表格，所以我快速搜索了动态草图表格符号/组件，但我找不到任何实际上是动态的东西。从某种意义上说，大多数表格都是“硬编码”的，它们的列和行是由线条形状定义的，而不是由单个单元格定义的，这使得在模板之外无法更改表格格式。

于是我出发了，创建了自己的动态速写表，并决定与大家分享(因为表格已经够难了吧？😅😵)

[![Example Table](img/3698f49688796f960b125457f4e7e840.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i4x_WWka--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/example-table-33fed95da8d740d0eddbe4a1b4049d88-0265e.jpg)

## 如何使用🛠

[**在这里下载素描模板**](https://github.com/whoisryosuke/ryosuke-gatsby-blog/tree/master/src/content/blog/2019/sketch-table-components/Dynamic-Sketch-Tables.zip)

1.  插入所需的单元格符号，并与表格行水平对齐。
2.  对表格行进行分组(以便更容易使用)。
3.  复制并粘贴该行，然后在下方对齐。重复，直到达到所需的行数。
4.  CMD/CTRL +点击每个单元格，动态改变侧边栏中的数据(比如文本甚至图标！)
5.  调整到你的❤️'s 愿望👌

[![Example Subtitle Table](img/ea4473cf703fce060390b9a29e614031.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r5ZZqK2N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/example-table-subtitles-492a8ec9b43ee9bfb54cf54aec26c048-0265e.jpg)

## 限制/警告🚦

### 复杂文本样式🖍

如果您希望某个单元格的部分文本是粗体或颜色不同于定义的文本样式，则必须将其从符号中分离出来。Sketch 还不能处理嵌套的文本样式。

[![Example Multiline alternating row Table](img/dafead795d4321c77dbf70273513787b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O2Z4uyv0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/example-table-multilines-eaf72eee5240e14f233ab00cba00e913-0265e.jpg)

### 交替表 rows⚪️⚫️

您可以为交替的表格行添加嵌套符号，但是删除一行会变得很麻烦，并且必须进入每个单元格/行并交换背景样式。我发现分离表格行组后面的交替背景更容易，并且您可以删除行并向上移动它们，而不用担心背景。

## 乒乓球为天🏓

我希望这有助于你将来设计一些表格，加快已经很痛苦的基于数据的设计过程。如果您有任何问题，请随时在 Twitter 上联系我或在此发表评论。让我知道如果你👀有故障吗👾

Cheers,
Ryo