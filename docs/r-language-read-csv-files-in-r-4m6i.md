# R 语言:用 R 读取 CSV 文件

> 原文：<https://dev.to/maheshkay/r-language-read-csv-files-in-r-4m6i>

让我们讨论如何从 r 中读取 CSV 文件。大多数 office 用户仍然依赖 CSV 文件和 Excel 文件来存储原始数据。尽管很多人已经转向不同类型的云存储。

但是大多数云数据收集平台中的导出选项通常将 CSV 文件作为一个选项。因此，当我们作为一名 R 程序员希望读取数据时，我们很可能会遇到大量的 CSV 文件。

所以在这里我们将采取看简单的教程来实现这个任务。如果你希望观看本教程的视频说明，请查看如何读取 R 中的 CSV 文件。

[https://www.youtube.com/embed/lEddKmImD9I](https://www.youtube.com/embed/lEddKmImD9I)

让我们假设我们有一个名为 mydata.csv 的文件。

```
Item,Cost,Sold,Profit
Keyboard,$10.00,$16.00,$6.00
Monitor,$80.00,$120.00,$40.00
Mouse,$5.00,$7.00,$2.00 
```

现在我们有一些样本文件要处理，让我们试着用 R 读取这个文件。

```
dt = read.csv("mydata.csv") 
```

要在控制台上输出数据，现在只需调用 dt。

```
dt 
```

这应该会产生如下所示的输出。

```
Item,Cost,Sold,Profit
Keyboard,$10.00,$16.00,$6.00
Monitor,$80.00,$120.00,$40.00
Mouse,$5.00,$7.00,$2.00 
```

您还可以在 dt 中使用 head()函数，并尝试获得间距适当的数据的更好视图。

就是这样。这是读取 CSV 文件的非常简单的方法。还有一些其他功能可以帮助你操作和编写新的 CSV 文件，我将在另一篇文章中介绍。

我希望这有助于您阅读数据的 CSV 文件。