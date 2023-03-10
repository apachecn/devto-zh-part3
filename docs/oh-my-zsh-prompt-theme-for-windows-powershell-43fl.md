# 哦，我的 Windows Powershell 的 Zsh 提示主题！

> 原文：<https://dev.to/luisramos1337/oh-my-zsh-prompt-theme-for-windows-powershell-43fl>

这些天来，我的大部分编程都是在 mac 上完成的，但偶尔我的游戏桌面会成为一些代码攻击的试验场。因为我在终端上使用 git，所以在 windows 上我使用 powershell 而不是一个可爱的 zsh shell。

使用 powershell 并没有那么糟糕，但是我非常怀念在提示符下没有 git 分支的日子。因为我真的很喜欢 Mac 上的 [Oh My Zsh](https://ohmyz.sh/) 主题，这里有一个片段可以改变你的 power shell 提示符！

```
function prompt {
\$ESC = [char]27

$p = Split-Path -leaf -path (Get-Location)
  $branch = $(git symbolic-ref -q HEAD) -replace "refs/heads/"
  if ($branch) {
$branch = "$ESC[34mgit:($ESC[0m$ESC[31m$branch$ESC[0m$ESC[34m)$ESC[0m "
}

"$ESC[1m$ESC[32m$([char]0x279C)$ESC[0m $ESC[36m$p$ESC[0m $branch\$ESC[0m"
} 
```

该代码位于`$profile`文件中，该文件可能需要创建:

```
new-item -itemtype file -path $profile -force
notepad $PROFILE 
```

记事本应该打开，你可以复制粘贴剪贴在那里！