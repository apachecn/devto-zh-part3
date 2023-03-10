# 保存 Outlook 电子邮件

> 原文：<https://dev.to/licyp/save-outlook-emails-ip3>

在 GitHub 上: [SaveOutlookEmails](https://github.com/licyp/SaveOutlookEmails)

# 保存 outlook 电子邮件

保存和备份 Outlook 帐户和项目(电子邮件、约会、附件等)。)到本地驱动器上。

## 目的

在我的 Outlook 中，只有最近三个月的电子邮件可以离线查看，其余的都被存档并转移到我的在线存档——Name@Company.com 的账户中。即使连接到网络，存档的帐户也只显示邮件正文的前 200 多个字符，没有附件。这意味着 Outlook 搜索不会从存档的帐户中找到任何内容。

我对这个问题的解决方案是将所有账户的所有邮件保存到我的桌面上，这样我就可以在 Windows Explorer 中进行搜索:在邮件正文和附件中进行搜索。

## 解

**SaveOutlookEmails** 将 Outlook 中的账户保存到桌面文件夹中。
T3![ProgressBar](img/fb27ecb79050fa9995f7d5d745ec775f.png)T5】

*   保持离线电子邮件最新日期:Outlook 启动时自动运行**保存 Outlook 电子邮件**(在 Outlook 启动时*启用宏*当出现*提示时，“Microsoft Office 已发现潜在的安全问题。”*)
*   保存存档账户:在选定的文件夹中运行 **SaveOutlookEmails** (需要一段时间，可以在午餐时间或晚上运行，详见*警告*)

Outlook 的文件夹结构保持不变，文件以日期时间前缀和简短主题命名。
[![Folder](img/71823894176cb81ebdf25a7fb9720d47.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bm58xxzc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9rj261gpl99xvzzvo6zm.jpg)