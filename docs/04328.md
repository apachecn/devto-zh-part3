# Tmux -跨会话的快速窗口切换

> 原文：<https://dev.to/pratham/tmux-fast-window-switching-across-sessions-3n0c>

### Tmux 窗口切换

Tmux 已经使得在一个会话中切换窗口变得很容易，然而，从另一个会话切换到窗口却很难。为了使窗口在会话间快速切换，我们可以利用 [fzf](https://github.com/junegunn/fzf)

*   使用`fzf-tmux`

```
 tmux list-windows -a -F "#S:#I-#W" | fzf-tmux | cut -d "-" -f 1 | xargs tmux switch-client -t 
```

Enter fullscreen mode Exit fullscreen mode

*   用`.tmux.conf`装订

```
 bind -n ^f run-shell "tmux list-windows -a -F \"##S:##I-##W\" | fzf-tmux | cut -d \"-\" -f 1 | xargs tmux switch-client -t" 
```

Enter fullscreen mode Exit fullscreen mode