# 在 Windows Server 上处理. tar.gz 文件

> 原文：<https://dev.to/jermdavis/dealing-with-tar-gz-files-on-windows-server-2ikk>

最近有几次，我发现自己需要将打包在`.tar.gz`档案中的文件部署到服务器上。在你的桌面上，这不是太大的问题——你只需运行你喜欢的第三方工具的安装程序，或者[使用新的 Unixy shell](https://docs.microsoft.com/en-us/windows/wsl/install-win10) 就可以了。但是在客户端服务器上，安全性可能更高，并且您并不总是可以选择运行任何旧的安装程序。所以我需要一个替代品…

为了寻找灵感，我搜索了一下，发现[一个关于堆栈溢出的线程](https://stackoverflow.com/questions/38776137/native-tar-extraction-in-powershell)，它暗示有一个用于处理`.tar`文件的 [PowerShell 扩展。它基于 7Zip 的库——但是它不需要安装完整的 7Zip 工具集，并且可以直接从 PowerShell 模块提要中获取。](https://github.com/thoemmi/7Zip4Powershell)

这似乎是一个很好的起点——但是答案中的代码还需要一些工作。所以我以此为基础，编写了自己的脚本来处理`.tar.gz`档案。

## 主脚本

为了有用，脚本需要接收一个要提取的文件和一个要放入结果的文件夹。这很容易通过声明几个强制参数来实现:

```
[cmdletbinding(SupportsShouldProcess=$True)]  param(  [Parameter(Mandatory=$True)]  [string]$FileToExtract,  [Parameter(Mandatory=$True)]  [string]$TargetFolder,  [int]$BufferSize  =  1024  ) 
```

Enter fullscreen mode Exit fullscreen mode

这些参数还声明该脚本将通过顶部的`cmdletbinding()`属性来遵循“`-whatif`”参数。它为提取`.gzip`流时使用的文件 IO 缓冲区的大小声明了一个可选参数——稍后会详细介绍。

脚本的逻辑相当简单。首先，它需要对将要处理的文件进行一些基本的验证。首先它可以测试文件是否实际存在:

```
if(!(Test-Path  $FileToExtract))  {  throw  "Source file '$FileToExtract' does not exist"  } 
```

Enter fullscreen mode Exit fullscreen mode

然后它可以测试它是否有正确的扩展名:

```
if(!$FileToExtract.EndsWith(".tar.gz",  "CurrentCultureIgnoreCase"))  {  throw  "Source file '$FileToExtract' does not have a .tar.gz extension"  } 
```

Enter fullscreen mode Exit fullscreen mode

一旦它满意了，整个处理过程就被分解成六个操作。前两个确保源文件有一个绝对路径，然后计算出隐藏在`.tar.gz`文件中的`.tar`文件的正确名称。

一旦完成，真正的工作是扩展 GZip 数据以获得`.tar`文件。那可以用母语来做。我马上会讲到的 Net 代码。然后，它必须确保安装了用于处理`.tar`文件的扩展——如果还没有“7 zip4 PowerShell”PowerShell 模块的话，就抓取它的副本。然后可以调用它来提取数据，最后删除临时的`.tar`文件:

```
$FileToExtract  =  Resolve-Path  $FileToExtract  $tarFile  =  Calculate-TarFileName  $FileToExtract  Expand-GZip  $FileToExtract  $tarFile  $BufferSize  Ensure-7Zip  Extract-Tar  $tarFile  $TargetFolder  if  ($PSCmdlet.ShouldProcess($tarFile,'Remove temporary tar file'))  {  Remove-Item  $tarFile  } 
```

Enter fullscreen mode Exit fullscreen mode

所有这些函数中都有什么？请继续阅读…

## 扩展 GZip 的东西

第一步是计算出 GZip 文件输出的名称——这是一个 tar 文件。这很简单，因为它只是意味着从输入文件名:
中去掉最后的“`.gz`

```
function  Calculate-TarFileName  {  param(  [Parameter(Mandatory=$true)]  [string]  $targzFile  )  $targzFile.Substring(0,  $targzFile.LastIndexOfAny('.'))  } 
```

Enter fullscreen mode Exit fullscreen mode

扩展 GZip 文件需要更多的工作。这很大程度上[抄袭了上面提到的堆栈溢出线程](https://stackoverflow.com/a/42165686/847953)的一个答案——有一些改进。首先，它现在理解了`-whatif`并且在那种情况下不会实际生成新文件。其次，它添加了一些代码来启用进度条。除此之外，它基本上只是使用标准来处理`GzipStream`。网名:

```
function  Expand-GZip  {  [cmdletbinding(SupportsShouldProcess=$True)]  param(  [Parameter(Mandatory=$true)]  [string]$infile,  [Parameter(Mandatory=$true)]  [string]$outFile,  [int]$bufferSize  =  1024  )  $fileSize  =  Original-GzipFileSize  $inFile  $processed  =  0  if  ($PSCmdlet.ShouldProcess($infile,"Expand gzip stream"))  {  $input  =  New-Object  System.IO.FileStream  $inFile,  ([IO.FileMode]::Open),  ([IO.FileAccess]::Read),  ([IO.FileShare]::Read)  $output  =  New-Object  System.IO.FileStream  $outFile,  ([IO.FileMode]::Create),  ([IO.FileAccess]::Write),  ([IO.FileShare]::None)  $gzipStream  =  New-Object  System.IO.Compression.GzipStream  $input,  ([IO.Compression.CompressionMode]::Decompress)  $buffer  =  New-Object  byte  while($true){  $pc  =  (($processed  /  $fileSize)  *  100)  %  100  Write-Progress  "Extracting tar from gzip"  -PercentComplete  $pc  $read  =  $gzipstream.Read($buffer,  0,  $bufferSize)  $processed  =  $processed  +  $read  if  ($read  -le  0)  {  Write-Progress  "Extracting tar from gzip"  -Completed  break  }  $output.Write($buffer,  0,  $read)  }  $gzipStream.Close()  $output.Close()  $input.Close()  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然而，制作一个有用的进度条需要知道最终流的大小。但是谷歌在这里再次拯救了我，它把我引向了这个描述实现这个的 C#代码的 CodeProject 帖子。原来你只需要验证它实际上是一个 GZip 流(查看前三个字节),然后找到最后四个字节得到一个`Int32`,这是原始长度:

```
function  Original-GzipFileSize  {  param(  [Parameter(Mandatory=$true)]  [string]  $gzipFile  )  $fs  =  New-Object  System.IO.FileStream  $gzipFile,  ([IO.FileMode]::Open),  ([IO.FileAccess]::Read),  ([IO.FileShare]::Read)  try  {  $fh  =  New-Object  byte  $fs.Read($fh,  0,  3)  |  Out-Null  # If magic numbers are 31 and 139 and the deflation id is 8 then this is a file to process  if  ($fh[0]  -eq  31  -and  $fh[1]  -eq  139  -and  $fh[2]  -eq  8)  {  $ba  =  New-Object  byte  $fs.Seek(-4,  [System.IO.SeekOrigin]::End)  |  Out-Null  $fs.Read($ba,  0,  4)  |  Out-Null  return  [int32][System.BitConverter]::ToInt32($ba,  0)  }  else  {  throw  "File '$gzipFile' does not have the correct gzip header"  }  }  finally  {  $fs.Close()  }  } 
```

Enter fullscreen mode Exit fullscreen mode

所以`Expand-GZip`函数可以在遍历流的块时使用它来计算完成百分比…

我之前提到过，在这里，整个脚本有一个用于处理的块大小选项。这里默认为 1KB，因为这是我抄袭的代码中的内容，但是您可以传递更大的块大小，以牺牲速度来换取内存使用。

但是一旦完成，最初的`.tar.gz`文件旁边会有一个`.tar`。

## 应付着。焦油文件

最初的[堆栈溢出线程包含了一个答案，表明 PowerShell 的“7Zip4PowerShell”模块](https://stackoverflow.com/a/46876070/847953)是这里最简单的方法。这段代码将它分成两个任务——一个任务是确保模块可以在本地使用，另一个任务是实际使用它。

该线程讨论了使用该模块的两种方法。一种是手动将需要的文件复制到本地，脚本拾取并使用这些文件，另一种是要求`Install-Package` commandlet 从公共提要中获取这些文件。为了搞笑，我决定把两者结合起来，因为我可以看到两者都有用的场景。

因此，如果您将包的文件放到脚本旁边的“7Zip4Powershell”文件夹中，它会发现它们并使用这个本地副本。要获取这些文件，您可以运行

```
Save-Module  -Name  7Zip4Powershell  -Path  . 
```

Enter fullscreen mode Exit fullscreen mode

根据提示。这种方法在高度安全的服务器上最有用，在这些服务器上，管理员希望在对您的工作使用的所有文件进行任何操作之前对它们进行审查。你可以把这个脚本和那个模块的文件都交给调查，它们只需要复制到服务器上就可以使用了。

或者，如果该文件夹不存在，它将从公共提要中提取模块。当你被允许使用时，这就是“零努力”方法。你不需要做任何额外的工作——如果还没有为你安装的话，它会自动载入正确的代码。和以前一样，逻辑被包装起来以支持`-WhatIf`。

```
function  Ensure-7Zip  {  param(  [string]$pathToModule  =  ".\7Zip4Powershell\1.9.0\7Zip4PowerShell.psd1"  )  if  (-not  (Get-Command  Expand-7Zip  -ErrorAction  Ignore))  {  if(Test-Path  $pathToModule)  {  if  ($PSCmdlet.ShouldProcess($pathToModule,"Install 7Zip module from local path"))  {  Write-Progress  -Activity  "Installing the 7Zip4PowerShell module"  "Using local module"  -PercentComplete  50  Import-Module  $pathToModule  Write-Progress  -Activity  "Installing the 7Zip4PowerShell module"  "Using local module"  -Completed  }  }  else  {  if  ($PSCmdlet.ShouldProcess("PowerShell feed",'Install 7Zip module'))  {  Write-Progress  -Activity  "Installing the 7Zip4PowerShell module"  "Using public feed"  -PercentComplete  50  $progressPreference  =  'silentlyContinue'  Install-Package  -Scope  CurrentUser  -Force  7Zip4PowerShell  >  $null  $progressPreference  =  'Continue'  Write-Progress  -Activity  "Installing the 7Zip4PowerShell module"  "Using public feed"  -Completed  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

目前这段代码是硬编码到这个模块的当前版本中的——如果有机会的话，让它更灵活是我的工作之一…

一旦安装了代码，运行提取的实际命令很简单:

```
function  Extract-Tar  {  [cmdletbinding(SupportsShouldProcess=$True)]  param(  [Parameter(Mandatory=$true)]  [string]  $tarFile,  [Parameter(Mandatory=$true)]  [string]  $dest  )  if  ($PSCmdlet.ShouldProcess($tarFile,"Expand tar file"))  {  Expand-7Zip  $tarFile  $dest  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在 7Zip 命令之上添加的是“`-WhatIf`”逻辑，因为默认情况下似乎不支持它。

## 在行动…

安装好之后，你就可以把文件解压到你想要的内容了:

[![](img/b3746e9be0264ca6444951989c5e58b5.png)](https://jermdavis.files.wordpress.com/2019/05/extracttargz.png)

如果你通过了`-WhatIf`，那么它会告诉你它会做什么，但什么也不做:

[![](img/4957d80a94fdfea52f504421bfe32bc3.png)](https://jermdavis.files.wordpress.com/2019/05/extracttargzwhatif.png)

如果你认为对你有用的话，我已经把全部代码放在一个摘要里了…