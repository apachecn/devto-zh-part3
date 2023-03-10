# R 语言:用 R 读取 XML 文件

> 原文：<https://dev.to/maheshkay/r-language-read-xml-files-in-r-5h54>

让我们讨论一下如何在 r 中读取 XML 文件。人们在很多地方都会用到 XML 数据。通常一些特定的数据集被存储到 XML 中。

在 JSON 文件像今天这样流行之前，许多公司都提供 XML 导出选项。因此，您将会发现许多将数据存储在 XML 文件中的遗留文件。所以我们要看看如何用 R 语言读取这些数据。

可以看教程的视频说明- [如何读取 R](https://www.youtube.com/watch?v=1cM_ZNZ9hhE) 中的 XML 文件。

[https://www.youtube.com/embed/1cM_ZNZ9hhE](https://www.youtube.com/embed/1cM_ZNZ9hhE)

XML 包

为了读取 XML 文件并操作数据，您需要使用 XML 包。您可以用下面的代码安装这个包。

```
install.packages("XML") 
```

假设我们有一个名为 *mydata.xml* 的 XML 文件。您可以从[示例文件](https://www.w3schools.com/xml/simple.xml)中获取 XML 数据。

现在我们可以开始导入包了。

```
library("XML") 
```

我们需要添加的下一个包如下。

```
library("methods") 
```

让我们命名输入文件并导入文件。

```
dt <- xmlParse(file = "mydata.xml") 
```

所以这里有一个名为“dt”的数据变量。您可以按如下方式打印输出。

```
print(dt) 
```

这是您在控制台中得到的输出。

```
<breakfast_menu>
<food>
<name>Belgian Waffles</name>
<price>$5.95</price>
<calories>650</calories>
</food>
</breakfast_menu> 
```

我希望这篇文章能帮助你从 R 语言的文件中读取 XML 数据。