# 修正了在 VS 代码中无法连接到 Docker 调试器的问题

> 原文：<https://dev.to/azure/fixing-issue-when-you-can-t-connect-to-docker-debugger-in-vs-code-34po>

我以前写过关于调试的博客。VS 代码中的. NET Docker 容器但是最近我遇到了一个容器的问题，我有一个. NET 核心应用程序无法连接调试器，错误如下:

```
Executing: docker.exe exec -i sunshine-functions sh -s < /home/aaron/.vscode-remote/extensions/ms-vscode.csharp-1.19.1/scripts/remoteProcessPickerScript
Linux
stderr: sh: 1: ps: not found
Error Message: Command failed: docker.exe exec -i sunshine-functions sh -s < /home/aaron/.vscode-remote/extensions/ms-vscode.csharp-1.19.1/scripts/remoteProcessPickerScript
sh: 1: ps: not found 
```

Enter fullscreen mode Exit fullscreen mode

问题的症结在于，它无法列出我需要从 VS 代码中挑选的进程。

我用作基础图像的图像是 [Azure Functions 主机](https://hub.docker.com/_/microsoft-azure-functions-base)，确切地说是`mcr.microsoft.com/azure-functions/dotnet`，结果这个特殊的图像中没有 [`ps`](http://man7.org/linux/man-pages/man1/ps.1.html) ！

谢天谢地，这是一个简单的修复，你需要使用`apt install`安装`procps`，当然假设你的映像来自支持`apt`的发行版。😉

一旦`ps`被安装到您的映像中，您现在就可以列出进程，然后调试您的映像。