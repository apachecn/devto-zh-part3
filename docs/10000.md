# 指南:如何在 5 分钟内排除 Windows 任务计划程序故障？

> 原文：<https://dev.to/xpolog/guide-how-to-troubleshoot-windows-task-scheduler-in-5-minutes-2fi7>

摘要

如果您负责管理组织的 Windows 服务器，那么简单地监控和管理您的机器就是一项全职工作。

Windows 任务计划程序等工具通过自动执行任务来帮助您保持服务器正常运行。尽管你非常依赖日程安排程序，但它有时会让你失望，而且往往是在最糟糕的时候。

遵循以下步骤或免费下载 XpoLog 7，并自动对您的预定任务进行故障排除-[https://www.xplg.com/download](https://www.xplg.com/download)。

当在本地机器上运行的单个任务失败时，您可以毫不费力地修复它。在大型组织中，任务要复杂得多，并且在成百上千个远程主机上运行。

Windows server 为您提供全面的日志记录工具来定位和修复问题>[https://www . log Gly . com/ultimate-guide/trouble shooting-with-windows-logs/](https://www.loggly.com/ultimate-guide/troubleshooting-with-windows-logs/)。

即使有了这些工具，您知道哪些日志是可用的吗？它们告诉了您什么？

阅读本文后，您将能够主动识别 Windows 任务计划程序问题，并使用 Windows 事件日志和相关数据来解决实际问题。

在各节之间跳过:

*   了解计划任务-[https://www . xplg . com/windows-server-windows-task-scheduler/#了解](https://www.xplg.com/windows-server-windows-task-scheduler/#understanding)
*   预定事件疑难解答-[https://www . xplg . com/windows-server-windows-task-scheduler/#疑难解答](https://www.xplg.com/windows-server-windows-task-scheduler/#troubleshooting)
*   如何在 5 分钟内解决任务问题[https://www . xplg . com/windows-server-windows-task-scheduler/# bonus](https://www.xplg.com/windows-server-windows-task-scheduler/#bonus)

Windows 任务计划程序:了解计划的任务
Windows 任务是一组动作，它们使系统管理和维护过程自动化；例如安装补丁、审计、备份存储介质或处理安全问题。

您可以使用任务计划程序用户界面或使用 PowerShell 或 the.Net 框架以编程方式创建和计划任务。

一旦创建了任务，就可以使用任务计划程序服务来计划任务的执行。

任务计划程序服务是一项 Windows 服务，允许您管理、计划和监视任务。

每个任务包括以下组件:

常规信息:描述任务的元数据，例如任务的名称、描述和位置。触发器:在特定时间安排任务执行的条件，或响应特定标准的条件。
行动:实现任务预期结果的一个或多个行动的列表。
安全主体:定义任务执行所需的安全凭证、允许的访问级别和系统特权。
条件:决定任务何时可以运行，即仅在目标主机空闲或接通电源时运行。
设置:配置任务如何运行，包括何时重新启动失败的操作，以及允许任务运行多长时间。
历史:记录任务执行数据。
Windows 任务计划程序–计划事件故障排除
让我们看看当计划任务无法运行时会发生什么，以及我们如何使用事件日志来定位和修复问题。

步骤 1:了解全局
要找到任务失败的直接原因，请打开事件查看器并找到事件。

双击事件会打开一个对话框，告诉我们问题的直接原因。它提供事件的来源、ID、级别和类别。该对话框还告诉我们事件是何时记录的，发生在哪台机器上。
任务计划程序未启动任务-“\ Microsoft \ Windows \ MemoryDiagnostic \ ProcessMemoryDiagnosticEvents”，因为同一任务的实例“{ 92 E4 BD 81-96af-4a 12-987 f-3 e83d 80 DD 116 }”已在运行。
日志名称:Microsoft-Windows-Task scheduler/Operational
来源:Microsoft-Windows-Task scheduler
日期:10/28/2018 下午 1:21:28
事件 ID: 322
任务类别:启动请求被忽略，实例已经运行
级别:警告
关键字:

用户:系统
计算机:BILBO.mordor.local

第 2 步:深入了解
要获得详细的上下文信息以帮助理解事件发生的原因，您可以跨多个 Windows 事件日志(evtx)文件使用 Windows 记录的相关事件。

日志的系统部分提供了这些附加环境数据的摘要，可以帮助您解决问题，例如任务的进程 ID、运行它的线程以及它的安全 ID。

guid = " { de 7b24 ea-73 c8-4 a09-985d-5 bdadc fa 9017 } "/>
322
0
3
322
0
0x 80000000000 "

Two hundred thousand two hundred and forty-one

Microsoft-Windows-task scheduler/Operational
Bilbo . mordor . local

步骤 3:理解事件上下文
要理解事件的实际性质并获得上下文线索，您需要查看日志文件的 EventData 部分。

在这里，我们可以看到任务未能运行的部分原因与内存问题有关。

\ Microsoft \ Windows \ MemoryDiagnostic \ ProcessMemoryDiagnosticEvents
{ 92e 4 BD 81-96AF-4a 12-987 f-3e 83d 80 DD 116 }

第 4 步:通过调查相关日志了解全貌
在大多数情况下，调查单个日志文件本身不足以发现和修复特定的问题。

因为许多计划问题都与安全相关，所以最好从检查与安全相关的审核日志开始，例如 Windows 安全事件日志。

Privileges: SeTcbPrivilege
审核失败 10/28/2018 13:21:28 比尔博·莫尔多微软-Windows-Security-Auditing 13056 4673 调用了特权服务
使用敏感特权使用/非敏感特权使用 0x00000000000D10EB
比尔博.莫尔多. local 调用了特权服务。

在这种情况下，Windows 安全事件告诉我们任务失败的原因与更广泛的安全问题有关，日志告诉我们该事件试图执行需要 SeTcbPrivilege 级别权限的操作。

该任务执行的操作被分配了较低的权限级别，因此，计划运行该任务的主机阻止了它的运行。

除了显示由于任务被分配了特权而导致任务失败之外，它还显示指示问题根本原因的信息。

进程:
进程 ID: 0x0000000000000EBC
进程名称:C:\ Windows \ System32 \ task hostw . exe

在这里，我们可以看到安全日志指出问题是由一个名为 taskhostw.exe 的可执行进程引起的。

日志向我们显示了可执行文件的进程 ID 和进程的完整路径。

在最后一步，我们将理解为什么这些信息很重要，以及我们如何使用它们。

第五步:研究问题
现在我们知道了问题的根源，让我们利用现有的资源去了解更多。

通过搜索互联网，我们可以看到发现 taskhostw.exe 问题的日志。此过程中的问题通常与恶意软件利用或控制文件夹访问的有缺陷的 Windows 组件有关。

有关 taskhostw.exe 和相关问题的更多信息，请参见 Microsoft 的这篇文章。

结论:如何充分利用 Windows Server 日志记录？Windows 调度程序任务是完成工作的好方法。

在本文中，我们向您展示了什么是任务，以及它们是如何工作的。我们还根据以下步骤带您完成了一个五步流程:

如何使用事件查看器发现问题。深入探讨:如何阅读事件日志文件以了解更多信息。
理解事件上下文:利用事件数据理解问题的上下文。
全面了解:使用其他日志文件来定位根本原因。
研究问题:如何利用你收集的数据找到可行的解决方案。
现在，您可以处理涉及多台服务器、大量日志数据的复杂情况，以确保:

您的系统继续履行您组织的使命
您支持的用户可以完成他们的工作。

你可以下载 XpoLog，点击几下就能获得这些见解-[https://www.xplg.com/download/](https://www.xplg.com/download/)

如何在 5 分钟内排除任务故障

监视 Windows 任务计划程序操作并对其进行故障排除(以及在 Windows 日志中查找错误)的另一种方法是使用现成的解决方案，如 XpoLog。

使用 XpoLog 内置的 Windows 日志连接器，您可以在几分钟内流式传输 Windows 事件日志。

一旦您将 Windows 事件日志流式传输到 XpoLog 中(只需点击几下鼠标)，您就可以开始深入了解系统中正在发生的事情，而无需动一根手指。

了解有关我们的 Windows 应用程序的更多信息

XpoLog 全自动工具带给你什么？

XpoLog 提供了一个仪表板，显示它收集的 Windows 任务计划程序相关数据的概述。

在这里，您可以快速找到有多少任务未能运行及其原因，有多少任务成功运行，以及重要事件的详细信息。

深入每个部分以获取更多信息。

仪表板显示收集的 Windows 日志数据的图形。

为了帮助您准确地找到您需要的日志，它提供了强大的搜索功能。

找到相关事件后，您可以查看日志数据

XpoLog 不仅向您展示过去发生了什么，它还监视系统日志文件中常见的和不明显的错误。

XpoLog 的主动分析引擎不断分析日志文件。当检测到问题时，它会立即向系统管理员发送通知。

底线:XpoLog 还可以帮助您处理那些您从未想过要寻找或者从来不知道存在的问题。

这意味着您、您的团队和您的组织可以在用户注意到之前很久就解决问题，并开始发送帮助台请求。

你先在这里听到的！少处理支持，多关注你的任务。

XpoLog 还通过从整个组织的多台服务器收集所有系统日志来提供长期保留。

XpoLog 可以从一个或多个位置保存任何时间段的所有与 Windows 相关的日志。

此外，XpoLog 不受您组织的存储策略和限制的约束。

附加价值:这意味着 XpoLog 无限期地存储收集的日志数据，并使用它来帮助您发现和修复在较长时间内发生的问题。

这种方法允许你主动采取预防措施来避免潜在的问题，而不是不停地灭火。

XpoLog 无限期存储收集的测井数据

结论

XpoLog 为您提供端到端的日志数据自动化解决方案。

部署后，XpoLog 使您能够监控您的系统，并为您提供一个完整的解决方案，包括一个全面的分析应用程序。

该 Windows 应用程序包含仪表板和报告，其中包含关于以下方面的见解:

用户访问(登录/注销)。
您的 Windows 服务器的健康状况。
趋势。
异常现象。
错误。
问题多。
您的收获:Windows 在几分钟内记录可见性和洞察力，无需手动编写查询或构建报告。

此外，您将能够轻松控制和监控您的 Windows 环境，查看和比较主机活动，完全无需手动操作！

多酷啊。