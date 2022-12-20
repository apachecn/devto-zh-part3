# 巧克力自动安装窗口

> 原文：<https://dev.to/alicannklc/chocolatey-autoinstall-windows-50ko>

[套餐搜索](https://chocolatey.org/search?q=name)

```
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"

choco feature enable -n allowGlobalConfirmation

choco install vscode -fy

choco install 7zip -fy

choco install nodejs-lts -fy

choco install yarn -fy 
```

。蝙蝠格式化了。

运行 adminastator 自动安装软件包。

你喝咖啡。

他制作装置

感谢您的阅读。