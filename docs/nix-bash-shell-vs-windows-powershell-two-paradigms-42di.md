# *nix Bash shell 与 Windows PowerShell:两种范例

> 原文：<https://dev.to/hamza/nix-bash-shell-vs-windows-powershell-two-paradigms-42di>

*最初发布于[我的作品集](https://hamza-tam.gitlab.io)。*

PowerShell 和 Bash Shell 有很多不同之处。第一个是完整的脚本环境，第二个只是命令行解释器。一种适用于 windows 服务器的管理，另一种更适用于开发环境。第一次发现这些差异时，它们可能看起来很明显。但是真正吸引我的是 PowerShell 使用对象，而 Bash Shell 使用纯文本。

让我们来看一个简单的 linux 命令。

```
$ ls -l 
```

该命令的输出将以纯文本格式返回，该输入可以通过管道传输到另一个命令，如 *grep*

```
$ ls -l | grep Desktop 
```

在这个例子中，bash 将获取 *ls -l* 命令的输出，一个字符串解析它，寻找参数中设置的模式。

Windows PowerShell 的行为有所不同。让我们试着在里面重复同样的例子。

```
PS > dir 
```

现在，如果我们想在文本上寻找一个特定的模式，我们将把结果传送给 *Select-String* 命令。

```
PS > dir | Select-String -pattern "Desktop" 
```

乍一看，您会注意到输出显示方式的不同。与 grep 不同，grep 显示找到模式的行，并突出显示该模式。但这不仅仅是一种展示风格或选择。输出是匹配模式的对象。

为了了解这种行为，我们假设 linux 命令的输出是:

```
drwxrwxrwx 1 hamza hamza    4096 Nov  4 18:29 Desktop
drwxrwxrwx 1 hamza hamza    4096 Nov  4 17:15 Documents
drwxrwxrwx 1 hamza hamza    4096 Nov  4 17:15 Downloads    
drwxrwxrwx 1 hamza hamza    4096 Nov  4 17:15 Favorites
drwxrwxrwx 1 hamza hamza    4096 Nov  7 10:56 IntelGraphicsProfiles
drwxrwxrwx 1 hamza hamza    4096 Nov  4 17:15 Links
drwxrwxrwx 1 hamza hamza    4096 Sep 22 01:26 MicrosoftEdgeBackups
drwxrwxrwx 1 hamza hamza    4096 Nov  4 17:15 Music
drwxrwxrwx 1 hamza hamza    4096 Oct 20 12:26 OneDrive 
```

那么如果我们使用:

```
$ ls -l | grep drw 
```

我们将得到相同的输出，但是这次突出显示了 *drw* 。但是对于第一个 PowerShell 命令，如果我们得到:

```
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---       04/11/2018     17:15                Contacts
d-r---       04/11/2018     18:29                Desktop
d-r---       04/11/2018     17:15                Documents
d-r---       04/11/2018     17:15                Downloads 
```

并使用以下命令对该输出进行搜索:

```
PS > dir | Select-String -pattern "d-r" 
```

我们将得不到任何回报。因为列模式列是*dir*(*Get-child item*的别名)的输出对象的 Select-String 不可搜索的参数。

如果用错误的参数调用 Select-String，它将为迭代抛出的每个文件对象发送一条错误消息。

了解不同技术的差异非常有趣，这是一种发现不同的人如何对相同的问题给出不同解决方案的方式。通过了解每项技术的强大之处和不足之处，你会开始以一种全新的方式看待技术。