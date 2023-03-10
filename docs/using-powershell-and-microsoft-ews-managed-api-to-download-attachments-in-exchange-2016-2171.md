# 使用 Powershell 和微软 EWS 托管 API 在 Exchange 2016 中下载附件

> 原文：<https://dev.to/techspence/using-powershell-and-microsoft-ews-managed-api-to-download-attachments-in-exchange-2016-2171>

这篇博文最初出现在我的网站上。可以在这里查看([https://www . Spencer alessi . com/Using-Powershell-and-Microsoft-EWS-Managed-API-to-download-attachments-in-Exchange-2016](https://www.spenceralessi.com/Using-Powershell-and-Microsoft-EWS-Managed-API-to-download-attachments-in-Exchange-2016))。

您是否使用过无法将附件或报告直接保存到文件中的软件？是啊，我也是。令人沮丧。我对重复任务的自动化非常感兴趣，我在这个特殊问题上的挫败感促使我寻找解决方案。我发现，我可以使用我心爱的 Powershell，结合 Microsoft Exchange Web 服务托管 API，从我的(或任何其他)outlook 邮箱下载附件。因此，为了解决这个小问题并增加一点自动化，我创建了一个 Powershell 脚本，它从一个预定的任务中运行，将完成这个任务。这篇博客文章描述了这个 Powershell 脚本如何工作的细节。我不太聪明地称之为“EWSEmailAttachmentSaver”。

虽然这篇博客文章更多地是关于系统管理而不是安全，关于谁将在一个给定的组织中创建这种类型的脚本，但是我的观点是，构成一个好的系统管理员的品质和技能与构成一个好的信息安全从业者的品质和技能是重叠的。幸运的是，我为一个 IT 部门相对较小的组织工作，我有自由和自主权从事这样的项目。此外，我是从服务台一步步走上来的，我习惯于创建这些脚本和自动化流程，因为这是我过去 8 年来一直在做的事情。现在开始解释剧本..

但是首先要注意一点。

我认为确保在脚本中提供好的文档是非常重要的。随着我成长和发展自己的技能，我经常被提醒好的文档是多么重要。我会在另一篇文章中更详细地讨论这个问题。但是要知道，如果你计划在生产环境中使用脚本，请做好文档，使用通用语言和内置函数。它极大地有助于将来解决问题，尤其是如果别人接管了你的脚本。

## 资源

当我决定创建一个脚本来自动化我的一些日常报告时，我发现了一些非常有用的博客帖子。构建该脚本的两个主要博客帖子是:

*   [EWS 托管 API 和 Powershell 操作指南系列第 1 部分](https://gsexdev.blogspot.com/2012/01/ews-managed-api-and-powershell-how-to.html)

*   [编写一个简单的脚本流程，使用 EWS 托管 API 在 Exchange 2007/ 2010 中下载附件](https://gsexdev.blogspot.com/2010/01/writing-simple-scripted-process-to.html)

Glen 的例子和文章非常有助于理解 EWS 以及如何编写一些 Powershell 来使用 API。谢谢格伦。如果你有兴趣了解更多关于 Exchange 或 Office365 和 Powershell 的信息，一定要去看看他的博客。 [Glen 的 Exchange 和 Office 365 开发博客](https://gsexdev.blogspot.com)

## EWS 邮件附件保护程序

**要求**

该脚本要求:

*   Exchange 2007 或更高版本
*   Exchange Web 服务(EWS)托管 API 2.2

**用户定义的变量**

在脚本的顶部，在注释部分，您会看到一些用户定义的变量。如果您使用这个脚本，您的大部分更改将在这里发生。这都是很标准的东西。

这个脚本有几个功能:

**T2`Function LogWrite`**

使用`!(Test-Path $logpath)`,我首先检查日志文件的路径是否存在，如果不存在，我就创建它。如果它确实存在，我使用`Add-content` cmdlet 将我指定的信息发送到日志文件。

**T2`Function FindTargetFolder`**

该函数循环遍历已处理的文件夹路径，直到找到目标文件夹。在我的脚本中，我方便地将已处理的文件夹直接放在邮箱的根目录下。

有趣的小旁注，你邮箱的根被称为*顶级信息库*，有时会显示成这样`\\email@company.com`。

注意，我的脚本假设*处理的文件夹*是用户邮箱根目录的子文件夹(例如`\\email@company.com\ProcessedFolder`

我发现了一个有趣的小事实，如果您将*已处理的文件夹*更改为任何其他文件夹下的文件夹，包括收件箱，脚本需要稍微修改。

如果您的*已处理文件夹*是任何其他文件夹下的子文件夹，您必须适当地更改`$processedfolderpath`和`$tftargetidroot`。

要快速查看 outlook 文件夹位置，请右键单击 outlook 中的文件夹，然后单击“属性”。

*举例:处理的文件夹是根邮箱的子文件夹:* `Location: \\email@company.com\ProcessedFolder`

```
$processedfolderpath = "/ProcessedFolder"
$tftargetidroot = New-Object Microsoft.Exchange.WebServices.Data.FolderId([Microsoft.Exchange.WebServices.Data.WellKnownFolderName]::MsgFolderRoot,$mailbox) 
```

*例如，已处理的文件夹是收件箱的子文件夹:* `Location: \\email@company.com\Inbox\ProcessedFolder`

```
$processedfolderpath = "/Inbox/ProcessedFolder"
$tftargetidroot = New-Object Microsoft.Exchange.WebServices.Data.FolderId([Microsoft.Exchange.WebServices.Data.WellKnownFolderName]::Inbox,$processedfolderpath) 
```

**T2`Function FindTargetEmail`**

这是控制大多数脚本动作的主要驱动函数。本质上，这个函数循环通过使用我们的过滤器发现的电子邮件，我将在一分钟内解释，然后它:

1.  根据附件名称确定下载位置
2.  将附件保存到下载位置，然后关闭它
3.  将电子邮件标记为已读，然后将其移至已处理文件夹

正如你所看到的，有一些附件名称的分割和一些黑客行为，以确保我将文件移动到正确的每月文件夹。这只是我自己的强迫症，其实没必要。:)

## 使用交易所 EWS API

既然我已经解释了这些函数的作用，我们可以继续解释 Exchange EWS API 了。要了解更多信息，请参见[从](http://www.microsoft.com/en-us/download/details.aspx?id=42951)下载 Microsoft Exchange Web 服务托管 API 2.2。

**下载并安装 EWS 托管 API**

下载并安装 Exchange EWS API 组件后，您需要为要使用的 API 命名空间加载适当的 EWS dll。通过加载`Microsoft.Exchange.WebServices.Data`名称空间，我们可以访问大多数 EWS 类和方法。以下是你如何加载 EWS dll。

```
$dllpath = "C:\Program Files\Microsoft\Exchange\Web Services\2.2\Microsoft.Exchange.WebServices.dll"
[void][Reflection.Assembly]::LoadFile($dllpath) 
```

一旦加载了 Webservices dll，您就可以开始使用它了。要了解关于 EWS API 的更多信息，请参见:[微软 EWS 管理的 API 参考](http://msdn.microsoft.com/en-us/library/jj220535(v=exchg.80).aspx)。还要注意，有多个名称空间，例如，对于像自动发现和身份验证这样的事情，我建议如果您想了解更多信息或者想使用其他功能，可以回顾一下它们。

**创建一个 EWS 服务对象**

现在，您需要为目标邮箱创建一个 EWS 服务对象。有许多方法可以对 EWS API 进行身份验证。对于我的脚本，我选择只使用我的组织的自动发现 URL，这允许我使用运行脚本的用户进行身份验证。

```
$exchangeservice.UseDefaultCredentials = $true
$exchangeservice.AutodiscoverUrl($mailbox) 
```

这也方便了我用这个脚本创建一个计划任务。我可以相应地允许一个服务帐户，而不必担心脚本中的硬编码凭证。应不惜一切代价避免硬编码的信用凭证。

**绑定到收件箱**

现在您只需要绑定到用户的收件箱。还有几种方法可以做到这一点。我选择使用`WellKnownFolderName`枚举。`WellKnownFolderName`定义用户邮箱中使用的常用文件夹名称。

```
$inboxfolderid = New-Object Microsoft.Exchange.WebServices.Data.FolderId([Microsoft.Exchange.WebServices.Data.WellKnownFolderName]::Inbox,$mailbox)
$inboxfolder = [Microsoft.Exchange.WebServices.Data.Folder]::Bind($exchangeservice,$inboxfolderid) 
```

**配置搜索过滤器**

最后要做的事情之一是为我们的目标电子邮件创建一些搜索过滤器。这一次我们将使用结合了`IsRead`、`Subject`和`HasAttachments`字段的`EmailMessageSchema`类。我所针对的主题可以在用户定义的变量部分看到。我在寻找包含“路径报告”主题的电子邮件其他过滤器应该是不言自明的。

值得注意的一件很酷的事情是，你可以将这些过滤器链接在一起，在末尾添加一个`And`并从中创建一些逻辑。

一旦你创建了这些变量，你就可以把它们全部添加到一个集合中，并使用它来查找你的目标电子邮件。

```
$sfunread = New-Object Microsoft.Exchange.WebServices.Data.SearchFilter+IsEqualTo([Microsoft.Exchange.WebServices.Data.EmailMessageSchema]::IsRead, $false)
$sfsubject = New-Object Microsoft.Exchange.WebServices.Data.SearchFilter+ContainsSubstring ([Microsoft.Exchange.WebServices.Data.EmailMessageSchema]::Subject, $subjectfilter)
$sfattachment = New-Object Microsoft.Exchange.WebServices.Data.SearchFilter+IsEqualTo([Microsoft.Exchange.WebServices.Data.EmailMessageSchema]::HasAttachments, $true)
$sfcollection = New-Object Microsoft.Exchange.WebServices.Data.SearchFilter+SearchFilterCollection([Microsoft.Exchange.WebServices.Data.LogicalOperator]::And);
$sfcollection.add($sfunread)
$sfcollection.add($sfsubject)
$sfcollection.add($sfattachment) 
```

**“查看”结果**

我创建了一个视图过滤器来限制查询开销。我选择让这个脚本一次查看 10 个项目。这是我从使用 PowerShell 和 EWS 监控邮箱的[中找到的一个技巧。](https://seanonit.wordpress.com/2014/10/29/using-powershell-and-ews-to-monitor-a-mailbox/)

```
$view = New-Object -TypeName Microsoft.Exchange.WebServices.Data.ItemView -ArgumentList 10
$foundemails = $inboxfolder.FindItems($sfcollection,$view) 
```

然后我只要叫`FindTargetFolder($processedfolderpath)`和`FindTargetEmail($subject)`就完事了。

现在点击命令行，导航到您的脚本所在的文件夹，并使用`.\EWSEmailAttachmentSaver.ps1`运行它。确保在进入生产环境之前，总是在开发或测试环境中测试代码。测试测试测试！

如果有人从中获得了价值，我很想知道具体是什么。如果你对我上面写的任何东西有任何意见、问题或反馈，我很乐意在 Twitter 上继续对话。打我一下[@ tech Spence](http://twitter.com/techspence)T5】