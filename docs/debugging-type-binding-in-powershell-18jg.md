# 在 PowerShell 中调试类型绑定

> 原文：<https://dev.to/sheldonhull/debugging-type-binding-in-powershell-18jg>

我花了一些精力研究堆栈溢出中的类型绑定，以帮助回答 Chris Oldwood 提出的一个问题，这帮助我巩固了对调试更复杂场景(如 PowerShell 中的这种场景)的最佳方式的理解。

> [为什么这个 PowerShell 函数的默认参数会根据使用。或者&在里面调用一个命令？](https://stackoverflow.com/q/53860403/68698)

我对此进行了深入研究，这是我观察到的。

首先，为了清楚起见，我不认为您应该在基本比较中将 NullString 值视为 null。我也不知道你为什么需要这个，因为这通常是我从 c#开发中所期望的。在 PowerShell 中，您应该可以使用`$null`来完成大部分工作。

```
if($null -eq [System.Management.Automation.Language.NullString]::Value)
{
write-host "`$null -eq [System.Management.Automation.Language.NullString]::Value"
}
else
{
write-host "`$null -ne [System.Management.Automation.Language.NullString]::Value"
} 
```

Enter fullscreen mode Exit fullscreen mode

其次，问题不一定是因为呼叫运营商，即`&`。相反，我认为您正在处理底层的参数绑定强制。强数据类型无疑是 PowerShell 的一个薄弱环节，因为即使是显式声明的`[int]$val`也可能在编写`Write-Host $Val`时被 PowerShell 在下一行自动设置为字符串类型。

为了识别底层行为，我使用了`Trace-Command`函数([跟踪命令](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/trace-command?view=powershell-6))。

我将 Use-Dot 改为只调用函数，因为不需要写主机来输出字符串。

```
function Use-Ampersand
{
param(
[string]$NullString = [System.Management.Automation.Language.NullString]::Value
)
Format-Type $NullString
&cmd.exe /c exit 0
} 
```

Enter fullscreen mode Exit fullscreen mode

我修改了 Format-Type，也使用了左边被认为是更好的做法`$null`,这也是由于类型推断。

```
function Format-Type($v= [System.Management.Automation.Language.NullString]::Value)
{
if ($null -eq $v)
{
'(null)'
}
else {
$v.GetType().FullName
}
} 
```

Enter fullscreen mode Exit fullscreen mode

为了缩小数据类型的问题，我使用了下面的命令，尽管这不是我发现问题的地方。当被直接调用时，它们的工作方式是一样的。

```
Trace-Command -Name TypeConversion -Expression { Format-Type $NullString} -PSHost
Trace-Command -Name TypeConversion -Expression { Format-Type ([System.Management.Automation.Language.NullString]$NullString) } -PSHost 
```

Enter fullscreen mode Exit fullscreen mode

然而，当我使用 TypeConversion 跟踪运行函数时，它显示了转换中的差异，这可能解释了您观察到的一些行为。

```
Trace-Command -Name TypeConversion -Expression { Use-Dot} -PSHost
Trace-Command -Name TypeConversion -Expression { Use-Ampersand} -PSHost

# USE DOT
DEBUG: TypeConversion Information: 0 : Converting "" to "System.String".
DEBUG: TypeConversion Information: 0 : Converting object to string.
DEBUG: TypeConversion Information: 0 : Converting "" to "System.Object". <<<<<<<<<<<
DEBUG: TypeConversion Information: 0 : Converting ".COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.PY;.PYW;.CPL" to "System.String".
DEBUG: TypeConversion Information: 0 : Result type is assignable from value to convert's type 
```

Enter fullscreen mode Exit fullscreen mode

`OUTPUT: (null)`

```
# Use-Ampersand
DEBUG: TypeConversion Information: 0 : Converting "" to "System.String".
DEBUG: TypeConversion Information: 0 : Converting object to string.
DEBUG: TypeConversion Information: 0 : Converting "" to "System.String". <<<<<<<<<<<
DEBUG: TypeConversion Information: 0 : Converting null to "". <<<<<<<<<<<
DEBUG: TypeConversion Information: 0 : Converting ".COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.PY;.PYW;.CPL" to "System.String".
DEBUG: TypeConversion Information: 0 : Result type is assignable from value to convert's type 
```

Enter fullscreen mode Exit fullscreen mode

`OUTPUT: System.String`

值得注意的区别是在`Use-Ampersand`中，它显示了`Converting null to ""`对`Converting "" to "System.Object"`的陈述。

在 PowerShell 中，`$null <> [string]''`。空字符串比较会通过 null 检查，导致成功输出`GetType()`。

# 使用 PowerShell 的几点想法

为什么会这样，我不确定，但是在你投入更多的时间研究之前，让我提供一个基于艰苦学习的建议。

*如果开始处理由于试图在 PowerShell 中强制数据类型而导致的问题，首先要考虑 PowerShell 是否是合适的工具*

是的，你可以使用类型扩展。是的，你可以用。NET 数据类型，如`$List = [System.Collections.Generic.List[string]]::new()`和一些。可以实施 NET 类型的规则。然而，PowerShell 并不像 C#那样被设计成一种强类型语言。试图这样做将会导致许多困难。虽然我是 PowerShell 的超级粉丝，但我已经学会认识到它的灵活性应该得到赞赏，它的局限性应该得到尊重。

如果我真的有需要映射的问题，我会考虑我的方法。

也就是说，这是一项具有挑战性的调查，我不得不采取行动，并在事后提供我的 10 美分。

# 其他资源

在贴出我的答案后，我发现了另一个似乎相关的[答案](https://stackoverflow.com/a/51354791/68698)，并且也支持通常不使用`[NullString]`的说法，因为它在 PowerShell 中的用法并不是它的设计初衷。

*Stackoverflow 在 CC-BY-SA 下重新发布的特定内容*