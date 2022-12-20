# ZSH/VIM -编辑剪贴板内容

> 原文：<https://dev.to/voyeg3r/zshvim---editing-clipboard-content-5027>

我们日常工作中一个非常常见的任务是编辑最后一个命令或剪贴板内容，所以我决定创建一些 zsh 小部件和别名来简化这个过程。

## 在 vim 上编辑剪贴板内容

```
alias vcb='xclip -i -selection clipboard -o | vim -' 
```

Enter fullscreen mode Exit fullscreen mode

如果您想要一个 vim 暂存缓冲区，那么您不必担心在离开它之前保存它。

```
alias vcb="xclip -i -selection clipboard -o | vim -c 'setlocal buftype=nofile bufhidden=wipe nobuflisted noswapfile' -" 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您在终端上，想要编辑您在浏览器上复制的一些内容，只需键入:

```
vcb 
```

Enter fullscreen mode Exit fullscreen mode

它可以更简洁...

```
alias pbpaste='xclip -i -selection clipboard -o'
alias pbcopy='xclip -selection clipboard'
alias vimscratch="vim -c 'setlocal buftype=nofile bufhidden=wipe nobuflisted noswapfile'"

alias vcb="pbpaste | vimscratch -"

# copy last command to clipboard
alias last2cb='fc -ln -1 | pbcopy' 
```

Enter fullscreen mode Exit fullscreen mode

## ZSH 小部件

将这些 widgets 放在您的~/上。zshrc，不要忘记添加相关的别名

```
# Copy the most recent command to the clipboard
function _pbcopy_last_command(){
        fc -ln -1 | pbcopy
    }
zle -N pbcopy-last-command _pbcopy_last_command
bindkey '^x^y' pbcopy-last-command

# Edit content of clipboard on vim
function _edit_clipboard(){
    pbpaste | vimscratch -
}
zle -N edit-clipboard _edit_clipboard
bindkey '^x^v' edit-clipboard 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以使用:

```
 Ctrl-x Ctrl-y  .............. copy the last command to the clipboard
 Ctrl-x Ctrl-v  .............. edit clipboard content on vim 
```

Enter fullscreen mode Exit fullscreen mode

OBS:我已经改短了

```
vim - 
```

Enter fullscreen mode Exit fullscreen mode

到

```
vim -c 'setlocal buftype=nofile bufhidden=wipe nobuflisted noswapfile' - 
```

Enter fullscreen mode Exit fullscreen mode

优点是 vim 将使用一个暂存缓冲区，如果您不想保存它，就不必担心了。但是，如果您有“vimscratch”别名，就不要为此而烦恼了。

如果万一您的`xclip -i -selection clipboard -o`命令不起作用，您可以使用:`vim -c '0pu+'`来代替。

## 将当前 vim 缓冲区复制到剪贴板

```
:%y+

: ................. command
% ................. current file (buffer | file in memory)
y ................. yank
+ ................. clipboard register 
```

Enter fullscreen mode Exit fullscreen mode

进入 vim **插入模式**后，您可以插入剪贴板寄存器，输入:

```
Ctrl-r + 
```

Enter fullscreen mode Exit fullscreen mode

使用此快捷方式有一个问题，但我们可以通过将这些行添加到您的`~/.vimrc`或`init.vim`来解决

```
" avoid clipboard hacking security issue
" http://thejh.net/misc/website-terminal-copy-paste
inoremap <C-R>+ <C-R><C-R>+ 
```

Enter fullscreen mode Exit fullscreen mode

### 将最后一条 vim 命令复制到剪贴板

```
:let @+ = @:

let ............. set a variable (in this case the clipboard)
@+ .............. clipboard register
=  .............. attribuition
@: .............. last command (register) 
```

Enter fullscreen mode Exit fullscreen mode

### 运行剪贴板内容作为 vim 命令

```
:@+ 
```

Enter fullscreen mode Exit fullscreen mode

当你想测试一个你在互联网上看到的 vim 函数时，上面的命令特别有用，只需复制它并运行它。例如，下面的函数旨在关闭除当前缓冲区之外的所有缓冲区。

```
function! CloseAllBuffersButCurrent()
    let curr = bufnr("%")
    let last = bufnr("$")
    if curr > 1    | silent! execute "1,".(curr-1)."bd"     | endif
    if curr < last | silent! execute (curr+1).",".last."bd" | endif
    echom "All other buffers unloaded!"
endfunction
command! -nargs=0 Bonly :call CloseAllBuffersButCurrent()
command! -nargs=0 Bo :call CloseAllBuffersButCurrent() 
```

Enter fullscreen mode Exit fullscreen mode

您可以运行`:@+`并调用该函数，然后再决定它是否是您想要的