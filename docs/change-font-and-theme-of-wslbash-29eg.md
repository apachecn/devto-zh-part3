# 更改 WSL/Bash 的字体和主题

> 原文：<https://dev.to/sun6eal/change-font-and-theme-of-wslbash-29eg>

# 主题

正如这个[帖子](https://blogs.msdn.microsoft.com/commandline/2016/09/22/24-bit-color-in-the-windows-console/)指出的，windows 控制台支持 24 位颜色。打开一个 cmd.exe 或 ubuntu bash 窗口，右击标题栏，选择`Properties`，你就可以开始了。

出于好奇，这个脚本([https://github.com/bitcrazed/24bit-color](https://github.com/bitcrazed/24bit-color))可以在你的终端上显示颜色条进行测试。

## 颜色工具

在这篇[文章](https://blogs.msdn.microsoft.com/commandline/2017/08/11/introducing-the-windows-console-colortool/)中，一个颜色工具可以帮助你快速应用一个`itemcolors`主题到你的终端。

## Tmux

Tmux 现在支持 256 种颜色，我们可以这样设置颜色:

```
# .tmux.conf
set -g status-fg colour255 
```

提示:您必须终止所有其他 tmux 会话才能看到您的更改。

如需完整的颜色名称列表，请查看此的[。](https://unix.stackexchange.com/questions/105568/how-can-i-list-the-available-color-names)

# 字体

Windows 控制台非常挑剔，我们只能选择为当前代码页设计的字体。windows 上 ubuntu 上的 bash 使用 utf8 代码页，cmd.exe 通常使用另一个代码页。我们不能永久地改变它，这很不正常。

经过许多肮脏的工作，我仍然不能让它工作，最后这个[字体](https://github.com/be5invis/Sarasa-Gothic)来拯救，设置为 cmd.exe 和 wsl/bash 分开。

这是屏幕截图。

[![screenshot](img/230725f4e1e286dbb667c02b8ca10fe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n88uJLOD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/38q8mdssx3z8nwn46x54.png)