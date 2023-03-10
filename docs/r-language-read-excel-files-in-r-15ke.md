# R 语言:用 R 读取 Excel 文件

> 原文：<https://dev.to/maheshkay/r-language-read-excel-files-in-r-15ke>

让我们讨论一下如何从 r 中读取 excel 文件。大多数办公室和小型企业的数据仍然保存在电子表格中。Microsoft Excel 是用来存储这些数据的流行电子表格软件之一。

这些天，甚至谷歌文档也在使用，但大多数人仍然喜欢基于离线桌面的电子表格文件，如 excel。

所以在这里，我们将看看用 R 语言读取 excel 文件的简单方法。可以查看一下这个教程的视频说明——[用 R 语言读取 Excel 文件](https://www.youtube.com/watch?v=V7SkBywk08I)。

[https://www.youtube.com/embed/V7SkBywk08I](https://www.youtube.com/embed/V7SkBywk08I)

我们将了解一些步骤，例如:

1.  安装读取 excel 文件的软件包
2.  读取 excel 文件
3.  享受

**1。安装 xlsx 包**

XLSX 包是 R 库中已知的包之一。这个软件包可以在 Windows，Mac，Linux 上运行，允许你读写 excel 文件。您也可以使用此库添加图像和图表。您可以使用以下命令安装 xlsx 包。

```
install.packages("xlsx") 
```

**2。读取 Excel 文件数据**

现在我们已经下载了这个包，是时候用代码进行测试了。这里有一个简单的代码片段来读取我们名为 *mydb.xlsx* 的 excel 文件。在这里，我们将读取文件中的第一页。

```
library(xlsx)  
fp <-  system.file("demo", "mydb.xlsx", package  =  "xlsx")  
res  <-  read.xlsx(fp, 1)  
head(res[, 1:6]) 
```

让我们讨论第 1 行的代码。

在第 1 行，我们基本上是在加载 xlsx 库。在第二行，我们在变量中加载文件，引用包 xlsx。在第三行，我们正在阅读 xlsx 文件的第一页。最后，我们从第 1 行到第 6 行开始显示记录，并在控制台上显示。

就是这样。

您可以查看 GitHub 上的官方软件包文档，以进一步操作 excel 文件。我希望这里解释的信息能帮助你以简单的方式使用 excel 文件。