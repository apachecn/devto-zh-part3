# 使用 FZF 和 Ripgrep 改进 Vim 和 Neovim 中的搜索

> 原文：<https://dev.to/matthewbdaly/improving-search-in-vim-and-neovim-with-fzf-and-ripgrep-579j>

不久前，我被要求对一个仍在使用 Subversion 的遗留项目进行一些更改。这很麻烦，因为我通常在文件中搜索的方法是使用 Tim Pope 的逃犯 Vim 插件作为`git grep`的前端，所以导航这个项目会比通常更难。因此，我开始寻找可供选择的搜索系统，其中一个不断出现的组合是 FZF 和瑞普瑞普，所以我决定尝试一下。FZF 是一个模糊的文件查找工具，用 Go 编写，而 Ripgrep 是一个速度极快的 grep，用 Rust 编写，默认情况下遵守 gitignore 规则。两者都被证明是非常有用的，它们现在已经成为了我系统的一部分。

在 Mac OS X 上，两者都可以通过自制软件获得，所以很容易安装。在 Ubuntu 上，Ripgrep 在仓库里，但是 FZF 不在，所以必须把它安装在我的主目录里。有一个用于 FZF 和 Ripgrep 集成的 [Vim 插件](https://github.com/junegunn/fzf.vim)，由于我使用 Vim-pluged，我可以通过在我的`init.vim`中添加以下内容，然后从 Neovim 运行`PlugUpdate`来安装它:

```
" Search
Plug '~/.fzf'
Plug 'junegunn/fzf.vim' 
```

Enter fullscreen mode Exit fullscreen mode

该插件提供了许多非常有用的命令，我将逐一介绍我最常用的命令:

*   `:Files`用于按名称查找文件。我以前用 Ctrl-P 来做这个，但是 FZF 更好更快，所以我几乎立刻就放弃了 Ctrl-P(虽然如果你想用同一个键，你可以把`:Files`映射到它上面)。
*   `:Rg`使用 Ripgrep 搜索文件中的内容，因此您可以搜索特定的字符串。这使得它成为《逃亡者》中`Ggrep`命令的绝佳替代品。
*   与 Ultisnips 一起工作，提供一个可过滤的可用片段列表，您可以插入这些片段，使其更加有用
*   `:Tags`允许您过滤和搜索整个项目中的标签
*   `:BTags`执行相同的操作，但仅在当前缓冲区中
*   `:Lines`允许您查找项目中的行并导航至它们。
*   `:BLines`做同样的事情，但是只在当前缓冲区中。

除了在 Neovim 中有用之外，FZF 在 Bash 中也很有用。您可以使用`Ctrl-T`来查找文件路径，它增强了标准的`Ctrl-R`历史搜索，使其更快、更容易导航。两者的性能都非常出色——它们工作得非常快，甚至在我维护的非常大的遗留项目上，或者在较慢的机器上，我从来没有发现自己在等待它们完成。这两个工具已经迅速成为我工作流程中不可或缺的一部分。