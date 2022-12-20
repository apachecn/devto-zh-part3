# 用 Vim(或任何文本编辑器)编辑 tmux 输出

> 原文：<https://dev.to/acro5piano/edit-tmux-output-with-editor-i1c>

[![image](img/a8df2e0f6fdc51097f1b043fc33c859c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aD4MVwuP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q7bros6kcqvxog7r4vc8.gif)

我们可以默认用`C-b [`滚动 tmux 输出，它提供了类似 vim 的按键绑定。

但是，有些绑定与我定制的 vim 不同。

所以我添加了一些脚本和设置来用 Vim(或任何其他文本编辑器)打开 tmux 输出。

好在 tmux 有一个好听的命令`capture-pane`！

```
#!/bin/bash

# $HOME/bin/vim-edit-tmux-output

file=`mktemp`.sh
tmux capture-pane -pS -32768 > $file
tmux new-window -n:mywindow "vim '+ normal G $'  $file" 
```

Enter fullscreen mode Exit fullscreen mode

`chmod +x $HOME/bin/vim-edit-tmux-output`

```
# $HOME/.tmux.conf

setenv -g PATH "$HOME/bin:$PATH"
set-option -g history-limit 20000

bind-key C-e run-shell "vim-edit-tmux-output" 
```

Enter fullscreen mode Exit fullscreen mode

然后按`C-b C-e`就可以打开你的 tmux 输出了！

另一个很好的特性是 tmux 在退出 vim 后会自动关闭 Vim 的新面板。