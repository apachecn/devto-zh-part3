# 在 tmux 启动时自动准备 windows

> 原文：<https://dev.to/yhara/automatically-prepare-windows-at-tmux-startup-4c6>

你有 tmux windows 的规则吗？例如，在哪个窗口启动编辑器？这是我的规则。

*   窗口 0: [转存](https://github.com/yhara/reposh)
*   窗口 1: vim
*   Window 2: zsh
*   窗口 3:服务器进程(即`rails server`)

这意味着每次我启动 tmux 时，我会打开 4 个窗口并运行它们。今天我学会了自动完成。

# 命令文件

第一步是编写一个 tmux 命令文件。这是我想要运行的 tmux 命令列表:

```
$ cat ~/proj/dotfiles/tools/tmux_multi
new-window
new-window
new-window
select-window -t 0
send-keys 'reposh' Enter
select-window -t 1
send-keys 'vim' Enter 
```

然后您可以在 tmux 启动时通过:
加载这个文件

```
$ tmux new-session ";" source-file ~/proj/dotfiles/tools/tmux_multi 
```

# 别名

当然这个太长了，记不住。所以我给我的`.zshrc` :
加了一个别名

```
alias tmux!='tmux new-session ";" source-file ~/proj/dotfiles/tools/tmux_multi' 
```

现在我的 tmux 通过`tmux!`设置 4 个窗口。

## 提示:设置 iTerm2 标签标题

如果您在 Mac 上使用 iTerm2，在启动 tmux 之前设置标签标题是很有用的。

```
alias tmux!='TAB_NAME=`ruby -e "print Dir.pwd.split(\"/\").last"`; echo -ne "\e]1;$TAB_NAME\a"; tmux new-session ";" source-file ~/proj/dotfiles/tools/tmux_multi' 
```