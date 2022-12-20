# 在 PowerShell 中连接路径

> 原文：<https://dev.to/devblackops/joining-paths-in-powershell-3e4c>

在 PowerShell 中，您经常会处理文件路径，并以编程方式构造它们来写入或读取文件。在 PowerShell 中建立文件路径有几种不同的方法，我将在下面介绍。

## 使用硬编码字符串

通过指定精确的硬编码路径来创建文件路径是非常常见的。

```
$path  =  'C:\mypath\to\foo.txt' 
```

然而，由于一些原因，这是有问题的。你会注意到我们在字符串中使用了`c:\`。这直接限制了它在 Windows 中的使用，因为`C:`驱动器的概念只存在于 Windows 中。

如果你还没听说，PowerShell 现在是跨平台的版本 6。我们需要开始思考，如果我们的脚本在 macOS 或 Linux 上执行，它们将如何运行。假设它们只能在 Windows 上执行将会限制它们的有用性，尤其是当我们打算为其他人发布它的时候。

这个例子的另一个问题是反斜杠`\`的使用。这可能是在 Windows 上分隔路径的主要方式，但是在类 Unix 操作系统上，使用正斜杠`/`。在大多数情况下，PowerShell 会在您运行的任何操作系统上为您规范化这一点，但人们经常忘记，Windows **也**支持正斜杠`/`作为路径分隔符，但这可能不是在所有情况下都有效。为了帮助确保我们的脚本在所有环境中都能工作，如果手动构建文件夹或文件路径，使用`/`是一个很好的实践。

此外，如果您需要在 Windows 上访问类似于`C:\mypath\to\foo.txt`的路径，或者在 macOS/Linux 上访问类似的路径，最好是混合使用环境和/或内置的 PowerShell 变量，使其更具弹性。在 PowerShell 6 中，新的布尔变量`$IsWindows`、`$IsLinux`和`$IsMacOS`会告诉你你在什么操作系统上，在 Windows 上，我们可以依靠`$env:SYSTEMDRIVE`返回系统驱动器。这通常是`C:\`，但在极少数情况下，也可能是另一个驱动器号。在类 Unix 操作系统上，我们可以使用 root `/`。我们可以根据操作系统使用这些变量来确定要使用的正确路径。

```
if  ($IsWindows)  {  $path  =  "$env:SYSTEMDRIVE/mypath/to/foo.txt"  }  else  {  $path  =  '/mypath/to/foo.txt'  } 
```

## 使用连接路径

PowerShell 包含 cmdlet `Join-Path`，用于获取多条路径并返回一条路径。这是一个更好的方法，因为`Join-Path`将确保根据上下文使用正确的路径分隔符。

下面的例子将在 Windows 上返回`C:\foo`。

```
$path  =  Join-Path  -Path  $env:SYSTEMDRIVE  -ChildPath  'foo' 
```

## 连接三条或更多的路径

通常，我们会遇到需要将两条以上的路径连接在一起的情况。我们**可以**做类似于
的事情

```
$path  =  "$path1/$path2/$path3" 
```

或者

```
$path  =  Join-Path  -Path  $path1  -ChildPath  "$path2/$path3" 
```

或者更糟:

```
$path  =  Join-Path  -Path  $path1  -ChildPath  (Join-Path  -Path  $path2  -ChildPath  $path3) 
```

不过，这些都不是特别优雅。

从 PowerShell 6 开始，`Join-Path`有了一个新的参数叫做`-AdditionalChildPaths`。此参数采用一个字符串数组，您可以使用该数组来包含所需数量的附加路径部分。这样，我们可以利用内置的 cmdlet，而不依赖于任何手动的字符串连接。

```
$path  =  Join-Path  -Path  $path1  -ChildPath  $path2  -AdditionalChildPaths  ($path3,  $path4) 
```

这些参数也在位置上起作用，因此如果您愿意，可以跳过指定参数名称。这在技术上违背了明确使用参数名的既定 PowerShell 风格准则，但对于常见的 cmdlets，这通常是可以接受的。

```
$path  =  Join-Path  $path1  $path2  $path3  $path4 
```

## 蘸入。网

PowerShell 的一大优点是能够深入了解。Net 当你需要额外的能力或灵活性。我最喜欢的构造文件路径的方法是使用。NET 的`[System.IO.Path]`类和`Combine()`方法。此方法接受两个或更多字符串，它将在一个操作中组合这些字符串，并且不依赖于任何手动字符串连接。

这个方法的伟大之处在于，它的工作方式类似于 PowerShell v6 的`Join-Path`和`-AdditionalChildPaths`参数，但也适用于较低版本的 PowerShell，使您的脚本或模块更加可移植。

```
$path  =  [IO.Path]::Combine($path1,  $path2,  $path3,  $path4) 
```

快乐的~~小径~~小径:)