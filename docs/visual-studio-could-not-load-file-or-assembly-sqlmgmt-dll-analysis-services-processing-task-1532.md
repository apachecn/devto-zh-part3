# Visual Studio:未能加载文件或程序集 sqlmgmt.dll 分析服务处理任务

> 原文：<https://dev.to/christophweigert/visual-studio-could-not-load-file-or-assembly-sqlmgmt-dll-analysis-services-processing-task-1532>

今天，我想在我们的测试服务器上部署一个多维分析服务解决方案，但是 Visual Studio 在加载处理任务用户界面后就崩溃了。异常为:“无法加载文件或程序集 sqlmgmt.dll 分析服务处理任务。”

我偶然发现的一个解决方法是复制粘贴文件。如果您使用的是专业版或企业版，则需要相应地更改第一个路径。

**复制自:**

c:\ Program Files(x86)\ Microsoft visual studio \ 2017 \ Community \ common 7 \ IDE \ Extensions \ Microsoft \ SQLCommon \ 140

**至**

c:\ Program Files(x86)\ Common Files \ Microsoft shared \ SQL Server 开发人员工具\140

重新启动 Visual Studio，然后就可以重新部署解决方案了。

## 一些更费时的解决方案是重新安装 SSDT 软件包

[SSDT 青奥会](http://blogs.msdn.com/b/ssdt/archive/2012/12/13/available-today-ssdt-december-2012.aspx)

[SSDT 青奥会](http://blogs.msdn.com/b/ssdt/archive/2014/03/25/sql-server-data-tools-for-sql-server-2014-is-available.aspx)

*引用:

[无法加载文件或程序集 sqlmgmt.dll 分析服务处理任务](https://developercommunity.visualstudio.com/content/problem/137098/could-not-load-file-or-assembly-sqlmgmtdll-analysi.html)

[尝试处理多维数据集](https://stackoverflow.com/questions/23473512/getting-assembly-error-when-attempting-to-process-cube-in-vs-2012)* 时出现程序集错误