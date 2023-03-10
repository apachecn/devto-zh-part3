# 为什么您的 PowerShell 计划任务没有运行？

> 原文：<https://dev.to/chustedde/why-is-my-powershell-scheduled-task-not-running-16ik>

这是我在我的部门的共享笔记中被引用最多的条目之一，所以它似乎是我的第一篇帖子面向更广泛受众的一个不错的选择！

## 需要签名

*   您的任务服务器需要对脚本进行签名吗？
    *   如果是的话，[你有有效的代码签名证书](https://devblogs.microsoft.com/scripting/hey-scripting-guy-how-can-i-sign-windows-powershell-scripts-with-an-enterprise-windows-pki-part-1-of-2/)和[你的脚本签名了吗](https://devblogs.microsoft.com/scripting/hey-scripting-guy-how-can-i-sign-windows-powershell-scripts-with-an-enterprise-windows-pki-part-2-of-2/)？
*   如果你的剧本签名了，签名还有效吗？下面是如何检查:`Get-AuthenticodeSignature -FilePath your-script.ps1`
    *   如果无效，请再次签名(在我的 PowerShell 配置文件中有一个快捷方式)

## 谁在管事？

*   无论用户是否登录，任务是否设置为运行？
*   您有运行计划任务的特定帐户吗？
    *   设置任务时是否保存了正确的密码？
    *   再试一次密码...我不会告诉任何人你试着打字而不是复制粘贴😅
*   用户/脚本运行帐户是否有正确的权限来完成您需要它做的一切？
    *   文件夹权限是一个常见问题

## 信任问题

*   运行脚本的帐户信任您的签名吗？
    *   您可能需要信任/重新信任您的签名，尤其是如果您最近获得了新的代码签名证书
*   如果可能，在运行任务的机器上，打开命令提示符(或 PowerShell！)并尝试以运行任务的用户身份启动 PowerShell:`runas /user:example.edu\taskrunner powershell.exe`
    *   尝试在刚刚弹出的新 PowerShell 窗口中运行您的脚本
    *   当它要求你信任你的脚本时，你说~~是~~ `[A]lways`！

我希望这至少能让一些人免受挫折。当然还有更奇怪的问题，但是这份清单可以帮助你解决最常见的问题！