# Excel 互操作技巧

> 原文：<https://dev.to/stipegrbic/an-excel-interop-trick-g3e>

昨天我度过了一个美好的夜晚，我的程序无声地失败了。该计划需要创建一个 Excel 文件，保存它，并导出为 PDF 格式。它还应该定期自动运行，作为一个预定的任务，对不对？是的，它在我的 Windows 10 电脑上运行良好，但在客户端 Windows Server 2012 上呢？不完全是。

### 怎么了？

我看到的第一件事是 Excel 文件被创建，但 PDF 没有。Excel 文件仍被后台的 Excel 程序使用。我的计划任务仍然显示为正在运行，并且在创建 Excel 文件后，日志文件没有任何新行。这意味着导出到 PDF 方法失败，但为什么呢？

我浏览了网页，发现很多抱怨和用户告诉你，你不能在没有用户登录的情况下自动执行 Excel 的预定任务。这很有意义，但是我的任务可以打开 Excel，生成一个文件并保存它。只是当它试图将其导出为 PDF 时，它停止了。并且仅在无人值守的情况下运行，即没有用户登录的情况下。

### 打印机

我不记得我尝试过解决这个问题的所有方法，但是几个小时后我遇到了这个 [StackOverflow 问题](https://stackoverflow.com/questions/10272415/excel-exportasfixedformat-pdf)。有一个答案指出，Excel 不能打印到 PDF，我应该将 Microsoft XPS document writer 设置为默认打印机。所以我试了试，结果成功了！这类问题让你觉得自己还活着，并让你在编写这些代码时不再感到舒适:)