# OmniSharp 和 VSCode 1.30.1

> 原文：<https://dev.to/nordyj/omnisharp-and-vscode-1-30-1-1odm>

我还没有在谷歌上看到任何关于这个的点击，但昨天我遇到了一个问题，我的 omni sharp(Visual Studio 代码的官方 C#插件)扩展拒绝正确加载。我卸载了扩展，重新启动了 VSCode，然后重新安装，再次重新启动了 VSCode。但是没有快乐。当 VSCode 在启用扩展的情况下启动时，OmniSharp 日志只是简单地说“访问被拒绝”。

我今天设法让它工作。我不得不像昨天一样卸载扩展。但是，我还必须物理删除 extension 文件夹，在 Windows 上，它位于 C:\Users{USER}中。vs code \ extensions \ ms-vs code . cs harp-1 . 17 . 1。删除 ms-vscode-csharp-1.17.1 文件夹，然后重新安装 OmniSharp 扩展，一切又正常了。

看起来好像在扩展的初始安装中有什么东西被破坏了。不幸的是，我没有日志。Windows 事件日志中什么都没有，直到我解决了应该在 VSCode 中打开 Chrome 开发者工具的问题，我才想到。希望如果其他人遇到这种情况，他们可以打开开发工具，对这个问题给出更多的见解。