# Windows 8 中的安全模式

> 原文：<https://dev.to/priteshusadadiya/safe-mode-in-windows-8--4a9b>

所以，我忘记了我的系统密码。我真傻😬。我有 2 个系统和 3 个 vpn 连接。一般来说，我记得我的密码，但今天突然，不知从哪里我忘记了哪个是哪个账户的密码？

长话短说，我浪费了大量的时间试图用 windows 修复模块重置它。(我第一次这么做)

试过`F8`、`Shift + F8`、`F5`都没用。:(

去了几个论坛，发现没有直接的方法可以在安全模式下打开 windows 机器。您必须首先运行一些命令(在 CMD 中),然后系统将在安全模式下重启。

于是，我启动了系统恢复选项，不知怎么就到了 CMD。

这里有一个需要在那里运行的命令，

```
bcdedit /set {default} safeboot minimal 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，这会将引导管理器设置为在安全模式下运行。

一旦设置好，你的系统将默认以安全模式运行，如果你想重置它(我的意思是正常运行)，你必须用这个命令删除上面的条目。(注意:除非您删除以上条目，否则系统将仅在安全模式下运行)

```
bcdedit /deletevalue {default} safeboot 
```

Enter fullscreen mode Exit fullscreen mode

# 你可能会问，我是如何恢复/重置密码的？😌

嗯，我尝试了不同的组合，终于记住了😂😂(想知道这是怎么发生的吗？)

浪费了整个星期天，但我想我还是学到了一些东西。:)

干杯，请