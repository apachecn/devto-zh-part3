# 我是怎样活着的

> 原文：<https://dev.to/aturingmachine/how-i-vim-4ha6>

> ![I Am Devloper profile image](img/f71ec87656afdf082a2e663bf011d85f.png)我是 developer@ iamdevloper![twitter logo](img/74b03a903e302feaec877e37a7689665.png)我用 Vim 到现在大概有 2 年了，主要是想不出怎么退出。2014 年 2 月 17 日下午 23:26[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=435555976687923200)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=435555976687923200)13829[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=435555976687923200)8673

# 简介

Vim 笑话和编辑器本身一样古老。然而，我已经每天使用 Vim 很多年了，主要用于快速编辑和编写提交消息。我想我会分享我的设置，也许其他人会觉得有帮助。

## 易用性

人们第一次使用 Vim 最难把握的是命令系统。这个系统可能非常强大，但对于大多数用例来说，它只是让新用户不知道如何保存他们刚刚编写的缓冲区。我们的第一个目标是将一些更常见的命令抽象成一个热键组合。

首先，我们需要在我们的主目录`touch .vimrc`中创建一个`.vimrc`，因为您可能不是 vim 用户，还需要使用您喜欢的编辑器进行以下更改。

我们将设置一个“领导”，这将是我们所有热键的开始。

```
let mapleader = "`" 
```

我更喜欢使用

```

键，因为它有点碍事，而且我不太可能不小心碰到它，不过你可以把它设置成你想要的。

现在我们要创建一个命令来写当前的缓冲区:

```
noremap <leader>w :w<cr> 
```

`noremap`是一个非递归的映射命令，这意味着我们不会将新命令链接在一起。这对初学者来说有点笨拙，很难调试。如果您对 vim 感到满意，那么可以随意使用递归方法`map`。

一旦我们告诉 vim 我们正在映射某个东西，我们就使用`<leader>`标签来使用后面跟有`w`的变量。所以击键

`` w`

会通过命令`:w`写入当前缓冲区，由`<cr>`回车执行。这一行相当于按了`esc`，打了`:w`，再打`enter`。

现在我们可以通过`noremap` :
编写一些更常用的命令

```
nnoremap <leader>a :wqall<cr>
nnoremap <leader>q :qall!<cr> 
```

这两个映射将添加

`` a`

来写退出所有打开的缓冲区，添加

`` q`

来退出所有打开的缓冲区。

现在我们可以轻松走出 vim 了！

## 更进一步

现在我们已经有了一些命令来方便地编写和退出 vim，我们可以开始让它感觉更像家了。下面是我自己`.vimrc` :
的摘录

```
" Turn on syntax highlightin
syntax on
" Turn on line numbers
set number
" Set the colorscheme to elflord
" See more colorschemes by entering command mode via esc then typing
" :colorscheme then pressing space and changing it via tab
colorscheme elflord

" Turn tabs into spaces and set them to be two spaces long
set expandtab
set tabstop=2
set softtabstop=2
set shiftwidth=2
set splitright

" highlight the current line of the buffer
set cursorline
" allow the mouse to be used to change cursor position
set mouse=a

" This block here executes the Vim Explorer on startup, opening the
" current directory tree in a small pane to the left
let g:netrw_banner = 0
let g:netrw_liststyle = 3
let g:netrw_browse_split = 4
let g:netrw_altv = 1
let g:netrw_winsize = 10
augroup ProjectDrawer
  autocmd!
  autocmd VimEnter * :Vexplore
augroup END

" `` should rotate split focus
noremap <leader><leader> <C-W><C-w> 
```

## 结论

我希望这可能有助于您更好地围绕 vim 工作，或者激起您的兴趣，开始根据您的喜好定制您自己的 vim！

如果你有任何很酷的 vim 技巧，请在评论中提出来，我很乐意升级我的 vim 游戏！